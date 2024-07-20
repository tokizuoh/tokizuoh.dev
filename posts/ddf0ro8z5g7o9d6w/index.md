---
title: "R.swift で文字列を取り扱う"
date: 2021-04-30T09:13:24+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
文字列をコード上のリテラルで管理しないのは精神衛生上良い。  
  
<!--more-->  
  
R.swiftの文字列のセットアップをやったことがなかったのでやってみた。  
めちゃくちゃ簡単だった。  
  
## 開発環境  
  
```bash
mac-cain13/R.swift@v5.4.0

> xcodebuild -version
Xcode 12.3
Build version 12C33
```
  
## セットアップ
  
プロジェクトディレクトリ配下に `.strings` 拡張子のファイルを配置。  
  
デフォルトの AppDelegate.swift の同層配下であればディレクトリがネストしていても取得可能。  
  
```
// message.strings
"one1" = "hoge";
"helloTo" = "Hello! %@!!";
```
  
## 使い方
  
```swift
let _ = R.string.message.one1()  // hoge
let _ = R.string.message.helloTo("YUKIO")  // "helloTo" = "Hello! YUKIO!!";
```
  
## 参考  
  
- [mac-cain13/R.swift: Strong typed, autocompleted resources like images, fonts and segues in Swift projects](https://github.com/mac-cain13/R.swift)  
  