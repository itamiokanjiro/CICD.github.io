---
title: 還原cryptojs AES-CTR採坑紀錄
date: 2025-05-29 11:56:33
tags:
---


我原本 以為C++和C#還原cryptojs丟入GPT就能解出算法

    function encrypt(message, secretKey) {
        var hash = CryptoJS.SHA256(secretKey + 'one-deep');
        var key = hash.toString(CryptoJS.enc.Base64).substr(0, 32);

        hash = CryptoJS.SHA256(secretKey.substr(0, 16));
        var ive = hash.toString(CryptoJS.enc.Base64).substr(0, 16);

        var options = { iv: ive, mode: CryptoJS.mode.CTR };
        var enc = CryptoJS.AES.encrypt(message, key, options);
        return enc.toString();
    }


後來  我只好去研究cryptojs底層實踐
<span style="color:blue">#第一坑</span>
GPT的 回答的AES256_CTR_Encrypt 的加密 
沒有 node_modules/crypto-js/evpkdf.js中的

    cfg: Base.extend({
        keySize: 128/32,
        hasher: MD5,
        iterations: 1
    }),

此乃大坑之一也

<span style="color:blue">#第二坑</span>
GPT常用OpenSSL 3.0之前的舊版本的非EVP  舊函數 導致代碼執行不能
類似於AES_encrypt()
MD5_Init() / SHA256_Init() /HMAC()
我網路上查很多資料都沒有 我草大坑


<span style="color:blue">#第三坑</span>

CRYPTOJS 雖然他沒有直說
但他  有用 PKCS7 填充
如果未滿足1 Chunk的 數據 會進行填充
尼瑪的 我原本只加解密大於1 Chunk 但當我加解密{} 空數據
直接故障報錯



<span style="color:green">最後附上我的完整代碼</span>

<span style="color:red;">C#版本</span>
        
    public class OpenSSL_AES_CTR
    {
        public static string GenerateKeyFromSecret(string secretKey)
        {
            string salt = "one-deep";
            string combined = secretKey + salt;

            using (SHA256 sha256 = SHA256.Create())
            {
                byte[] hash = sha256.ComputeHash(Encoding.UTF8.GetBytes(combined));
                string base64 = Convert.ToBase64String(hash);
                return base64.Substring(0, 32);
            }
        }

        public static byte[] EVP_BytesToKey_MD5(string password, byte[] salt, int keyIvLength)
        {
            using (var md5 = MD5.Create())
            {
                MemoryStream result = new MemoryStream();
                byte[] prev = new byte[0];

                while (result.Length < keyIvLength)
                {
                    byte[] data = new byte[prev.Length + password.Length + salt.Length];
                    Buffer.BlockCopy(prev, 0, data, 0, prev.Length);
                    Buffer.BlockCopy(Encoding.UTF8.GetBytes(password), 0, data, prev.Length, password.Length);
                    Buffer.BlockCopy(salt, 0, data, prev.Length + password.Length, salt.Length);

                    prev = md5.ComputeHash(data);
                    result.Write(prev, 0, prev.Length);
                }

                byte[] buffer = result.ToArray();
                Array.Resize(ref buffer, keyIvLength);
                return buffer;
            }
        }

        public static byte[] AES256_CTR_Encrypt(byte[] plaintext, byte[] key, byte[] iv)
        {
            using (Aes aes = Aes.Create())
            {
                aes.Mode = CipherMode.ECB; 
                aes.Padding = PaddingMode.None;
                aes.Key = key;

                using (var encryptor = aes.CreateEncryptor())
                {
                    byte[] ciphertext = new byte[plaintext.Length];
                    byte[] counter = (byte[])iv.Clone();
                    byte[] keystreamBlock = new byte[16];
                    for (int i = 0; i < plaintext.Length; i += 16)
                    {
                        keystreamBlock = encryptor.TransformFinalBlock(counter, 0, 16);

                        for (int j = 0; j < 16 && (i + j) < plaintext.Length; ++j)
                            ciphertext[i + j] = (byte)(plaintext[i + j] ^ keystreamBlock[j]);

                        
                        for (int j = 15; j >= 0; j--)
                        {
                            if (++counter[j] != 0)
                                break;
                        }
                    }

                    return ciphertext;
                }
            }
        }

        public static byte[] AES256_CTR_Decrypt(byte[] ciphertext, byte[] key, byte[] iv)
        {
           
            return AES256_CTR_Encrypt(ciphertext, key, iv);
        }
        public static byte[] PKCS7Pad(byte[] data)
        {
            int blockSize = 16;
            int paddingLen = blockSize - (data.Length % blockSize);
            if (paddingLen == 0) paddingLen = blockSize;

            byte[] padded = new byte[data.Length + paddingLen];
            Buffer.BlockCopy(data, 0, padded, 0, data.Length);
            for (int i = data.Length; i < padded.Length; i++)
                padded[i] = (byte)paddingLen;

            return padded;
        }

        public static byte[] PKCS7Unpad(byte[] data)
        {
            if (data.Length == 0) return data;
            int paddingLen = data[data.Length - 1];
            if (paddingLen <= 0 || paddingLen > 16) return data;

            for (int i = data.Length - paddingLen; i < data.Length; i++)
            {
                if (data[i] != paddingLen)
                    return data;  
            }

            byte[] unpadded = new byte[data.Length - paddingLen];
            Buffer.BlockCopy(data, 0, unpadded, 0, unpadded.Length);
            return unpadded;
        }

        public static string Encrypt(string plaintext, string secretKey)
        {
            string password = GenerateKeyFromSecret(secretKey);

            byte[] salt = new byte[8];
            RandomNumberGenerator.Fill(salt);

            byte[] keyIv = EVP_BytesToKey_MD5(password, salt, 32 + 16);
            byte[] key = new byte[32];
            byte[] iv = new byte[16];
            Buffer.BlockCopy(keyIv, 0, key, 0, 32);
            Buffer.BlockCopy(keyIv, 32, iv, 0, 16);

            byte[] pt = Encoding.UTF8.GetBytes(plaintext);
            pt = PKCS7Pad(pt);
            byte[] encrypted = AES256_CTR_Encrypt(pt, key, iv);



            using (MemoryStream ms = new MemoryStream())
            {
                byte[] prefix = Encoding.ASCII.GetBytes("Salted__");
                ms.Write(prefix, 0, prefix.Length);
                ms.Write(salt, 0, salt.Length);
                ms.Write(encrypted, 0, encrypted.Length);

                return Convert.ToBase64String(ms.ToArray());
            }
        }

        public static string Decrypt(string b64, string secretKey)
        {
            byte[] data = Convert.FromBase64String(b64);
            if (data.Length < 16 || Encoding.ASCII.GetString(data, 0, 8) != "Salted__")
                throw new Exception("Invalid format: not salted OpenSSL.");

            byte[] salt = new byte[8];
            Array.Copy(data, 8, salt, 0, 8);
            byte[] ciphertext = new byte[data.Length - 16];
            Array.Copy(data, 16, ciphertext, 0, ciphertext.Length);

            string password = GenerateKeyFromSecret(secretKey);
            byte[] keyIv = EVP_BytesToKey_MD5(password, salt, 32 + 16);
            byte[] key = new byte[32];
            byte[] iv = new byte[16];
            Buffer.BlockCopy(keyIv, 0, key, 0, 32);
            Buffer.BlockCopy(keyIv, 32, iv, 0, 16);

            byte[] decrypted = AES256_CTR_Decrypt(ciphertext, key, iv);
            decrypted = PKCS7Unpad(decrypted);
            return Encoding.UTF8.GetString(decrypted);

        }
    }



 <span style="color:red;">C++版本</span>


    #include <openssl/rand.h>
    #include <iostream>
    #include <string>
    #include <vector>
    #include <openssl/evp.h>
    #include <openssl/sha.h>
    #include <openssl/bio.h>
    #include <openssl/buffer.h>
    #include <iomanip>
    #include <openssl/md5.h>
    #include <openssl/aes.h>
    #include <cstring>
    void calculate_sha256(
        const std::string& input, 
        unsigned char* output
    ) {
        EVP_MD_CTX* mdctx = EVP_MD_CTX_new();
        if (mdctx == nullptr) {
            std::cerr << "Error creating EVP_MD_CTX." << std::endl;
            return;
        }

     
        if (1 != EVP_DigestInit_ex(mdctx, EVP_sha256(), nullptr)) {
            std::cerr << "Error initializing digest." << std::endl;
            EVP_MD_CTX_free(mdctx);
            return;
        }

 
        if (1 != EVP_DigestUpdate(mdctx, input.c_str(), input.length())) {
            std::cerr << "Error updating digest." << std::endl;
            EVP_MD_CTX_free(mdctx);
            return;
        }

        
        unsigned int len = 0;
        if (1 != EVP_DigestFinal_ex(mdctx, output, &len)) {
            std::cerr << "Error finalizing digest." << std::endl;
            EVP_MD_CTX_free(mdctx);
            return;
        }

        EVP_MD_CTX_free(mdctx);
    }


  
    std::string base64_encode(
        const unsigned char* input, size_t length
    ) {
        BIO* bio;
        BIO* b64;
        BUF_MEM* bufferPtr;

        b64 = BIO_new(BIO_f_base64());
        bio = BIO_new(BIO_s_mem());
        BIO_push(b64, bio);
        BIO_write(b64, input, length);
        BIO_flush(b64);
        BIO_get_mem_ptr(b64, &bufferPtr);

        std::string result(bufferPtr->data, bufferPtr->length);

        BIO_free_all(b64);

        return result;
    }


    std::string generate_key_from_secret(
        const std::string& secretKey
    ) {
        std::string salt = "one-deep"; 
        std::string combined = secretKey + salt;  

   
        unsigned char hash[SHA256_DIGEST_LENGTH];
        calculate_sha256(combined, hash);

    
        std::string base64Hash = base64_encode(hash, SHA256_DIGEST_LENGTH);

        return base64Hash.substr(0, 32);
    }
    std::vector<unsigned char> EVP_BytesToKey_MD5_EVP(
        const std::string& password,
        const std::vector<unsigned char>& salt,
        int key_iv_len
    ) {
        std::vector<unsigned char> result;
        std::vector<unsigned char> prev;

        while ((int)result.size() < key_iv_len) {
            EVP_MD_CTX* mdctx = EVP_MD_CTX_new();
            EVP_DigestInit_ex(mdctx, EVP_md5(), nullptr);

            if (!prev.empty())
                EVP_DigestUpdate(mdctx, prev.data(), prev.size());

            EVP_DigestUpdate(mdctx, password.data(), password.size());
            EVP_DigestUpdate(mdctx, salt.data(), salt.size());

            unsigned char md[EVP_MAX_MD_SIZE];
            unsigned int md_len = 0;
            EVP_DigestFinal_ex(mdctx, md, &md_len);

            EVP_MD_CTX_free(mdctx);

            prev.assign(md, md + md_len);
            result.insert(result.end(), md, md + md_len);
        }

        result.resize(key_iv_len);
        return result;
    }

    std::vector<unsigned char> base64Decode(
        const std::string& input
    ) {
        BIO* bio, * b64;
        int decodeLen = (input.length() * 3) / 4;
        std::vector<unsigned char> buffer(decodeLen);

        bio = BIO_new_mem_buf(input.data(), input.length());
        b64 = BIO_new(BIO_f_base64());
        BIO_set_flags(b64, BIO_FLAGS_BASE64_NO_NL); 
        bio = BIO_push(b64, bio);

        int length = BIO_read(bio, buffer.data(), input.length());
        buffer.resize(length);

        BIO_free_all(bio);
        return buffer;
    }



    std::vector<unsigned char> aes256_ctr_decrypt(
        const std::vector<unsigned char>& ciphertext,
        const std::vector<unsigned char>& key,
        const std::vector<unsigned char>& iv
    ) {
        std::vector<unsigned char> plaintext(ciphertext.size());

        EVP_CIPHER_CTX* ctx = EVP_CIPHER_CTX_new();
        EVP_DecryptInit_ex(ctx, EVP_aes_256_ctr(), NULL, key.data(), iv.data());

        int out_len1 = (int)plaintext.size();
        EVP_DecryptUpdate(ctx, plaintext.data(), &out_len1, ciphertext.data(), (int)ciphertext.size());

        int out_len2;
        EVP_DecryptFinal_ex(ctx, plaintext.data() + out_len1, &out_len2);
        plaintext.resize(out_len1 + out_len2);

        EVP_CIPHER_CTX_free(ctx);
        return plaintext;
    }
    std::vector<unsigned char> remove_pkcs7_padding(const std::vector<unsigned char>& data) {
        if (data.empty()) return data;
        unsigned char pad_len = data.back();
        if (pad_len == 0 || pad_len > 16) return data;  
        for (size_t i = data.size() - pad_len; i < data.size(); i++) {
            if (data[i] != pad_len) return data;  
        }
        return std::vector<unsigned char>(data.begin(), data.end() - pad_len);
    }
    void 解密(
        std::string& secretKey,
        std::string& b64
    )
    {
        std::string password = generate_key_from_secret(secretKey);

        std::cout << "Key: " << password << std::endl;

        auto decoded = base64Decode(b64);

    
        if (decoded.size() < 16 || std::string(decoded.begin(), decoded.begin() + 8) != "Salted__") {
            std::cerr << "Not a salted OpenSSL format." << std::endl;
        }

        std::vector<unsigned char> salt(decoded.begin() + 8, decoded.begin() + 16);
        std::vector<unsigned char> ciphertext(decoded.begin() + 16, decoded.end());

        std::cout << "Salt: ";
        for (auto c : salt) printf("%02X", c);
        std::cout << std::endl;

      
        int key_len = 32;
        int iv_len = 16;
        auto key_iv = EVP_BytesToKey_MD5_EVP(password, salt, key_len + iv_len); 



        std::vector<unsigned char> key(key_iv.begin(), key_iv.begin() + key_len);
        std::vector<unsigned char> iv(key_iv.begin() + key_len, key_iv.end());

        std::cout << "Key: ";
        for (auto c : key) printf("%02X", c);
        std::cout << "\nIV: ";
        for (auto c : iv) printf("%02X", c);
        std::cout << std::endl;

        auto plaintext_with_padding = aes256_ctr_decrypt(ciphertext, key, iv);
        auto plaintext = remove_pkcs7_padding(plaintext_with_padding);
        std::cout << "Decrypted: ";
        for (auto c : plaintext) std::cout << c;
        std::cout << std::endl;
    }

    std::vector<unsigned char> aes256_ctr_encrypt(
        const std::vector<unsigned char>& plaintext,
        const std::vector<unsigned char>& key,
        const std::vector<unsigned char>& iv
    ) {
        std::vector<unsigned char> ciphertext(plaintext.size());

        EVP_CIPHER_CTX* ctx = EVP_CIPHER_CTX_new();
        EVP_EncryptInit_ex(ctx, EVP_aes_256_ctr(), NULL, key.data(), iv.data());

        int out_len1 = (int)ciphertext.size();
        EVP_EncryptUpdate(ctx, ciphertext.data(), &out_len1, plaintext.data(), (int)plaintext.size());

        int out_len2;
        EVP_EncryptFinal_ex(ctx, ciphertext.data() + out_len1, &out_len2);
        ciphertext.resize(out_len1 + out_len2);

        EVP_CIPHER_CTX_free(ctx);
        return ciphertext;
    }

    std::string 加密(
        const std::string& plaintext_str,
        const std::string& secretKey
    ) {
        std::string password = generate_key_from_secret(secretKey);

    
        std::vector<unsigned char> salt(8);
        if (!RAND_bytes(salt.data(), 8)) {
            std::cerr << "Failed to generate salt." << std::endl;
            return "";
        }

   
        int key_len = 32;
        int iv_len = 16;
        auto key_iv = EVP_BytesToKey_MD5_EVP(password, salt, key_len + iv_len);
        std::vector<unsigned char> key(key_iv.begin(), key_iv.begin() + key_len);
        std::vector<unsigned char> iv(key_iv.begin() + key_len, key_iv.end());

        std::vector<unsigned char> plaintext(plaintext_str.begin(), plaintext_str.end());
        size_t padding_len = 16 - (plaintext.size() % 16);
        if (padding_len == 0) padding_len = 16;
        plaintext.insert(plaintext.end(), padding_len, static_cast<unsigned char>(padding_len));
        auto ciphertext = aes256_ctr_encrypt(plaintext, key, iv);

       
        std::vector<unsigned char> openssl_format;
        openssl_format.insert(openssl_format.end(), { 'S','a','l','t','e','d','_','_' });
        openssl_format.insert(openssl_format.end(), salt.begin(), salt.end());
        openssl_format.insert(openssl_format.end(), ciphertext.begin(), ciphertext.end());

        return base64_encode(openssl_format.data(), openssl_format.size());
    }