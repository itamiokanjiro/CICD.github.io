---
title: gh基礎
date: 2025-05-31 19:56:19
tags:
---
改成main

    gh api \
    -X PATCH \
    -H "Accept: application/vnd.github+json" \
    /repos/itamiokanjiro/itamiokanjiro.github.io \
    -f default_branch=main

22


    curl -X PATCH \
    -H "Authorization: token 我的token" \
    -H "Accept: application/vnd.github+json" \
    https://api.github.com/repos/itamiokanjiro/itamiokanjiro.github.io \
    -d '{"default_branch":"main"}'

gh登入 創建itamiokanjiro.github.io

   gh auth login
   gh repo create test.github.io --public --confirm

gh登出

    gh auth logout

強制設定看main

    gh api -X POST /repos/itamiokanjiro/test.github.io/pages \
    -f source.branch='main' \
    -f source.path='/'


刪除

   gh repo delete itamiokanjiro/test.github.io





GitHub.com -> SSH -> Yes -> 密碼 -> Paste an authentication token ->

