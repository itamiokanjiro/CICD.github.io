---
title: tmux基礎
date: 2025-05-30 21:34:34
categories:
  - linux

---

tmux attach -t 進入目標 名為hexo

    tmux attach -t hexo

tmux new -s創建一個會話 名為hexo

    tmux new -s hexo

tmux kill-session -t 刪除名為hexo的會話

    tmux kill-session -t hexo

進出

    ctrl+b 再按d






切換到編號1的視窗

    tmux select-window -t hexo:1  


刪除編號1的視窗

    tmux kill-window -t hexo:1
