---
title: C#基礎dotnet
date: 2025-05-30 21:37:38
tags:
categories:
  - 語言適應
---


用 snap 安裝 dotnet-sdk 
經典模式 --classic
表示這個 snap 需要較多系統權限，允許比較自由的存取系統檔案，類似傳統安裝方式

    sudo snap install dotnet-sdk --classic

建立一個別名 (alias)，讓你用更簡短的指令呼叫 snap 裡的程式。
使我能輸入dotnet 調用

    sudo snap alias dotnet-sdk.dotnet dotnet

驗證安裝

    dotnet --version

開啟

    dotnet 第二個網站.dll

windows中執行


    Add-Migration InitialCreate
    dotnet ef migrations add InitialCreate --project ./第二個網站/第二個網站.csproj --startup-project ./第二個網站/第二個網站.csproj


切換數據庫 從sql server切換到mysql

    將NuGet 換成調整成Pomelo.EntityFrameworkCore.MySql
    然後把報錯的Program.cs 調整成
    //builder.Services.AddDbContext<ApplicationDbContext>(options =>
    //    options.UseSqlServer(connectionString));
    builder.Services.AddDbContext<ApplicationDbContext>(options =>
        options.UseMySql(
            builder.Configuration.GetConnectionString("DefaultConnection"),
            ServerVersion.AutoDetect(builder.Configuration.GetConnectionString("DefaultConnection"))
        ));
    這樣
    然後 把報錯 的其餘代碼刪除即可
