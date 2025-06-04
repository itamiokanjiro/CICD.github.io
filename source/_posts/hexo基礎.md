---
title: hexo基礎
date: 2025-05-30 21:34:48
tags:
categories:
  - 網站部署基礎

---
hexo new創建一個新文章名為666

    hexo new "666"


hexo g建置   並且cp -r 複製所有public/ 目錄下的檔案 發送到/var/www/html3/

    hexo g && cp -r public/* /var/www/html2/

hexo 初始化

    hexo init

npm install 根據當前資料夾中的package.json 依賴進行更新

    npm install 

hexo server啟動  -i 開放0.0.0.0 端口4000 

    hexo server -i 0.0.0.0 -p 4000


npx 會先在本地 node_modules（專案資料夾的套件目錄）尋找 hexo，如果找不到，會臨時從網路下載並執行。

    npx hexo server -i 0.0.0.0 -p 4000


快速CICD

    npm install hexo-deployer-git --save
    hexo clean && hexo g && hexo d



開發 上傳

    deploy:
    type: git
    repo: https://github.com/你的帳號/你的repo.git
    branch: gh-pages


HEXO嵌入圖片

    ![網路層](/image/lay.jpg)


顏色

    <span style="color:blue">#換顏色</span>


