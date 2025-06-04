---
title: linux基本命令基礎
date: 2025-05-30 21:40:22
categories:
  - linux
---
重新啟動nginx

    sudo systemctl restart nginx

設定timedatectl的時間  亞洲台北

    sudo timedatectl set-timezone Asia/Taipei

開關 自動 更新時間工具

    sudo timedatectl set-ntp true
    sudo timedatectl set-ntp false

調整時間為

    sudo date +%T -s "19:59:00"


顯示時間

    date


顯示端口有無人使用

    sudo netstat -tuln | grep 4000
    COMMAND    PID USER FD   TYPE   DEVICE SIZE/OFF NODE NAME


顯示端口使用者進程

    sudo lsof -i :4000


砍進程

    sudo kill 

查看使用者有無密碼

    cat /etc/shadow

查看使用者能否遠程登入

    cat /etc/passwd
    getent passwd postgres

禁止postgres登入

    sudo usermod -s /usr/sbin/nologin postgres

windows用的

    $env:DISPLAY="localhost:0.0"
    Git bash
    ssh -Y -p 1998 hacker@192.168.1.104
    連接上 linux 包含介面
    apt install freerdp2-x11
    xfreerdp /v:10.129.43.4 /u:htb-student /p:HTB_@cademy_stdnt!


顯示目前工作&幹掉

    jobs
    kill %1