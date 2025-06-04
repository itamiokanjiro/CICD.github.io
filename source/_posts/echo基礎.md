---
title: 學習
categories:
  - linux
---

hex+ASCII模式 正常順序  且-n = 有沒有換行符號

    echo -n "cat /flag.txt" | hexdump -C
    
 
這裡是以 2 bytes（16 位元）一組來顯示，每組是 4 個十六進位數字 16-bit Word 模式

    echo -n "cat /flag.txt" | hexdump
    

這是 轉換ROT13

    echo "cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_uJdSftmh}" | tr 'A-Za-z' 'N-ZA-Mn-za-m'

這是轉成base64

    echo -n "cat /flag.txt" | base64
    

這是 將input轉換成echo "$input" | base64 -d 解密 連續6次 最終echo打印出input

    input="VmpGU1EyRXlUWGxTYmxKVVYwZFNWbGxyV21GV1JteDBUbFpPYWxKdFVsaFpWVlUxWVZaS1ZWWnVh
    RmRXZWtab1dWWmtSMk5yTlZWWApiVVpUVm10d1VWZFdVa2RpYlZaWFZtNVdVZ3BpU0VKeldWUkNk
    MlZXVlhoWGJYQk9VbFJXU0ZkcVRuTldaM0JZVWpGS2VWWkdaSGRXCk1sWnpWV3hhVm1KRk5XOVVW
    VkpEVGxaYVdFMVhSbFZrTTBKeldWaHdRMDB4V2tWU2JFNVdDbUpXV2tkVU1WcFhWVzFHZEdWRlZs
    aGkKYlRrelZERldUMkpzUWxWTlJYTkxDZz09Cg=="

    for i in {1..6}; do
    input=$(echo "$input" | base64 -d)
    done

    echo "$input"


這是透過bc將 56這個10進制值  轉換成 2進制   bc執行"obase=2; 56"

    echo "obase=2; 56" | bc


base64加上-d是base64解密  不加則是加密

    echo "cGljb0NURntNRTc0RDQ3QV9ISUREM05fZGVjYTA2ZmJ9Cg==" | base64 -d

這是 輸出這 4 個 ASCII 字元  e模式是可輸入  控制字符 \n 執行換行  一般來說是打印

    echo -e "\x34\x65\x63\x39"

| 控制字元| 意義            |
| ------ | --------------- |
| \n     | 換行            |
| \t     | tab             |
| \r     | carriage return |
| \xHH   | 十六進位字元     |
| \\     | 字元 ""         |



先將val賦予成 執行完base64 -d 的內容  在sed -E 的清理提取 完後 最後 解密後打印出來

    val=$(echo "YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclh6ZzJhMnd6TW1zeWZRPT0nCg==" | base64 -d)
    clean=$(echo "$val" | sed -E "s/^b'(.*)'$/\1/")
    echo "$clean" | base64 -d

|命令表| 意義|
| ------ | --------------- |
|s/  | 要找的模式/要取代成的東西/|
|^	| 開頭（字串開頭必須是）|
|b'	| 字面上的 b'|
|(.*)| 括號代表群組，把中間所有字元抓出來|
|'	| 字面上的結尾單引號|
|$	| 表示一定要在整行的結尾|
|\1	| 表示替換成剛才 (.*) 群組裡面抓到的內容|

xxd -p是將輸入內容轉為「純十六進位格式（plain hexdump）」，不帶位址、ASCII 解釋等欄位
    echo -n "ioplm" | xxd -p


od -An -tx1 使用 od（octal dump）工具： -An 表示不印出位址欄位 -tx1 表示以十六進位格式，每個位元組一組輸出
tr -d ' \n'把 od 輸出的空格和換行全部移除，只留下純 hex 字元
    echo -n "ioplm" | od -An -tx1
    echo -n "ioplm" | od -An -tx1 | tr -d ' \n'

這個xxd -p =先轉換  sed 每2個字元+空白   awk轉換 NF是欄數位 由後往前讀每個欄位，逆序輸出
    echo -n "ioplm" | xxd -p | sed 's/../& /g' | awk '{ for(i=NF;i>0;i--) printf $i }'


寫入目標檔案 md5sum 建立一個假的 md5sum 腳本  
chmod +x md5sum設為可執行檔
將當前目錄放進 PATH 開頭

    echo -e '#!/bin/bash\ncat "$@"\n/bin/md5sum"$@"' > md5sum
    chmod +x md5sum
    PATH=.:$PATH ./flaghasher


把當前目錄 . 放到環境變數 PATH 的最前面
    export PATH=.:$PATH