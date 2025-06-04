---
title: curl基礎
date: 2025-06-02 15:02:18
tags:
---

curl 發送https請求 
-O是下載

    curl -O -X 'GET' \
    'http://verbal-sleep.picoctf.net:49884/heapdump' \
    -H 'accept: */*'

| 參數             | 說明                               |
| -------------- | -------------------------------- |
| `-X`           | 指定 HTTP 方法（如 `GET`、`POST`、`PUT`） |
| `-o [檔名]`      | 將下載內容儲存為指定檔案                     |
| `-O`           | 以原始檔名儲存下載內容                      |
| `-d`           | 傳送 POST/PUT 表單資料（`key=value`）    |
| `-H`           | 加入自訂 HTTP 標頭（Header）             |
| `-u`           | HTTP Basic 認證（`user:pass`）       |
| `-F`           | 表單格式上傳檔案（`-F "file=@檔案"`）        |
| `-v`           | 顯示詳細傳輸過程（verbose 模式）             |
| `-k`           | 忽略 HTTPS 憑證錯誤                    |
| `-I`           | 只抓 HTTP Response Header          |
| `-L`           | 自動跟隨 HTTP 轉址（redirect）           |
| `-b`           | 傳送 Cookie（可接字串或 cookie 檔）        |
| `-c`           | 將 Cookie 儲存至檔案                   |
| `--cookie`     | 同 `-b`，傳送 cookie 字串              |
| `--resolve`    | 自訂 DNS 解析（格式：`host:port:IP`）     |
| `--limit-rate` | 限制傳輸速率（如：`100k`、`1m`）            |
| `--retry`      | 請求失敗時自動重試次數                      |
| `--socks5`     | 使用 SOCKS5 Proxy                  |
| `-x`           | 使用 HTTP Proxy（注意是小寫）             |
| `--max-time`   | 最長請求時間（秒）                        |
| `--trace`      | 輸出詳細除錯 trace 到檔案                 |
| `-X DELETE`    | 指定 HTTP 方法為 `DELETE`，用於刪除資源 |
| `-k `          | 忽略 SSL 憑證錯誤                            |
| `--cert cert.pem --key key.pem`   | 使用客戶端憑證連線 |
