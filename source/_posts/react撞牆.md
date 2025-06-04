---
title: react撞牆
date: 2025-05-31 21:12:05
tags:
categories:
  - 網站部署基礎
---


react用的

    npm install --save-dev gh-pages


.git資料夾的遠端推送設定優先於所有設定  所以先看遠端 再改設定

    git remote -v
    git remote set-url origin git@github.com:itamiokanjiro/test.github.io.git


一鍵部屬

    rm -rf build && npm run build && npm run deploy


靜態檔案package.json設定

    "homepage": "https://itamiokanjiro.github.io/test.github.io",
    "devDependencies": {
    "gh-pages": "^6.3.0"
    }

改推送分之為main
    "scripts": {
        "start": "react-scripts start",
        "build": "react-scripts build",
        "test": "react-scripts test",
        "eject": "react-scripts eject",
        "predeploy": "npm run build",
        "deploy": "gh-pages -d build -b main"
    },
    