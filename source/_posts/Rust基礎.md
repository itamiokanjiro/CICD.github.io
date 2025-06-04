---
title: Rust基礎
date: 2025-06-02 00:52:31
tags:
categories:
  - 語言適應
---

Rust 官方的開發流程是透過 cargo（等於 Rust 的專案管理工具 + 套件管理 + 編譯工具）來管理所有事情，
所以不推薦直接透過RUST 編譯執行  建議使用cargo


檢測全局版本

    rustc --version
    cargo --version


RUST 使用hello world

    fn main() {
        println!("Hello, Rust!");
    }


RUST 編譯執行

    rustc main.rs
    ./main

cargo 建立專案

    cargo new hello_rust
    cd hello_rust
    cargo run