---
title: git基礎
date: 2025-05-30 21:40:55
tags:
---
git clone 下載目標檔案

    git clone https://github.com/D-Sketon/hexo-theme-reimu.git
    git clone https://github.com/D-Sketon/reimu-template
    
查看歷史

    git log 
    git log --perl-regexp --author='^(?!picoCTF <ops@picoctf\.com>).*$'

切換到 

    git switch --detach b562f0b425907789d11d2fe2793e67592dc6be93
    git switch master
    git switch feature/part-1


顯示分支

    git branch


git教學

    git remote remove origin
    git remote add origin git@github.com:itamiokanjiro/itamiokanjiro.github.io.git
    git remote set-url origin git@github.com:itamiokanjiro/itamiokanjiro.github.io.git


產生金鑰


    ssh-keygen -t rsa -b 4096 -C "a234123234a@gmail.com"
    cat ~/.ssh/id_rsa.pub



    ssh-keygen -t ed25519 -C "a234123234a@gmail.com"
    cat ~/.ssh/id_ed25519.pub


設定作者

    git config user.name "你的 GitHub 使用者名稱"

設定信箱

    git config user.email "你的 GitHub 郵箱"

查看修改 狀況

    git status   


刪除跟蹤

    git rm -r --cached 跟CTF無關/



推送遠端

    git push -u origin main


更新用

    git commit -m "更新"


測試連接

    ssh -T git@github.com
