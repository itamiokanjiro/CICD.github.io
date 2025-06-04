---
title: GO基礎
date: 2025-06-02 00:54:55
tags:
categories:
  - 語言適應
---



到這邊安裝 [官網] (https://go.dev/dl/)
    

查看版本  

    go version

測試安裝是否成功

    package main

    import "fmt"

    func main() {
        fmt.Println("Hello, Go!")
    }



測試

    go run main.go


編譯
    go build main.go
    ./main.exe