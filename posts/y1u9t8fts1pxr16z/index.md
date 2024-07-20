---
title: "KeyPathを試す"
date: 2020-11-20T22:55:37+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
  
バックスラッシュ。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> /Applications/Xcode_12_1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift --version
Apple Swift version 5.3 (swiftlang-1200.0.29.2 clang-1200.0.30.1)
Target: x86_64-apple-darwin19.6.0
```
  
## KeyPathとは  
> Use key-path expressions to access properties dynamically.  
> ― [Key-Path Expressions | Apple Developer Documentation](https://developer.apple.com/documentation/swift/swift_standard_library/key-path_expressions)  
  

  
プロパティに動的にアクセスするために使う。  
  
## コード  
  
```swift
struct A {
    let name = "a-san"
    let b = B()  // bのname(b-san)にアクセスしたい
}

struct B {
    let name = "b-san"
}

let a = A()
let path = \A.b.name
print(a[keyPath: path])  // b-san

```
とりあえずKeyPathを用いてプロパティにアクセスしてみた。  
  
　
  
```swift
let a = A()
print(a.b.name)  // b-san
```
  
KeyPathを使わずにアクセスするときは上記のようになる。  
  
　
  
```swift
let a = A()
let firstPath = \A.b
let lastPath = \B.name

let path = firstPath.appending(path: lastPath)
print(a[keyPath: path])  // b-san
```
  
動的にパスを生成してアクセスしてみた。  
プロパティまでのパスを変数で定義できるのが旨味に見えた。  
  
## 参考  
- [Key-Path Expressions | Apple Developer Documentation](https://developer.apple.com/documentation/swift/swift_standard_library/key-path_expressions)
