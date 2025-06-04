---
title: apk逆向基礎
date: 2025-05-30 20:26:37
tags:
---


Android Stadio的Logcat監聽手機
jadx開啟目標應用 全為java語言 特徵  每個檔案只能有一個類  類與檔案名相同

R.string.password代表 res/values/string.xml/password
條件運算子（? :）

    public class FlagstaffHill {
        public static native String fenugreek(String str);
        public static String getFlag(String input, Context ctx) {
            String password = ctx.getString(R.string.password);
            return input.equals(password) ? fenugreek(input) : "NOPE";
        }
    }
