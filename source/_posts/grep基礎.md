---
title: grep基礎
date: 2025-05-30 21:20:06
tags:
categories:
  - linux
---

grep只能找內容
-r = 循環所有子目錄 
-a 是讓 grep 把二進位檔（binary files）當成純文字檔來讀取
~/ 是要搜尋的目錄路徑 ~ 代表使用者家目錄  如果目錄有空格要用\ 區隔
    grep -a -r "uuuuutest" ~/
    grep -r "flaghasher" /

尋找檔案名為flaghasher的檔案

    find / -iname "*flaghasher*"