---
title: chrome console
date: 2025-05-30 20:26:37
tags:
---

先定義一個表scrambledSegments
再定義一個地圖orderMap
再定義一個方法restorePassword

    const scrambledSegments = [
    'lz_b',
    'no_c',
    'CTF{',
    'pico',
    '706c',
    '5}',
    'ts_p',
    'lien',
    ];

    const orderMap = [3, 2, 1, 7, 6, 0, 4, 5];

    function restorePassword(scrambled, order) {
    let result = '';
    for (let i = 0; i < order.length; i++) {
        result += scrambled[order[i]];
    }
    return result;
    }

    const recovered = restorePassword(scrambledSegments, orderMap);
    console.log('還原密碼:', recovered);
