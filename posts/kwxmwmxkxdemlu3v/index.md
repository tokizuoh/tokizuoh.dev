---
title: "callAsFunction を試す"
date: 2021-10-30T20:48:24+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
[swift-evolution](https://github.com/apple/swift-evolution) 読んでいくぞ  
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 13.0
Build version 13A233
# Swift 5.5
```
  
## callAsFunction とは
  
```swift
struct Subtracter {
    var base: Int

    func callAsFunction(_ x: Int) -> Int {
        return base - x
    }
}

let subtracter = Subtracter(base: 11)
subtracter(1)   // 10 (= 11 - 10)
subtracter(10)  //  1 (= 11 - 1)
```
  
構造体で callAsFunction という命名でメソッドを書けば、関数のように呼び出すことが可能。  
  
プロポーザル は[SE-0253](https://github.com/apple/swift-evolution/blob/main/proposals/0253-callable.md)。 既に採択されて実装済み。Swift 5.2から利用可能。  
  
## 何が嬉しい？  
  
構造体の実体のメソッド呼び出しの記述量が少なくなる。  
　
  
```swift
// Before
struct Subtracter {
    var base: Int
    
    func subtract(_ x: Int) -> Int {
        return base - x
    }
}

let subtracter = Subtracter(base: 11)
subtracter.subtract(1)  // 10 (subtracter.subtractでくどい)
```
  
導入前は上記のように書く必要があった。  
  
　
  
```swift
// After
struct Subtracter {
    var base: Int

    func callAsFunction(_ x: Int) -> Int {
        return base - x
    }
}

let subtracter = Subtracter(base: 11)
subtracter(1)  // 10 (スッキリ！)
```
  
callAsFunctionの導入後は、構造体の実体からのメソッド呼び出しが、関数呼び出しだけで済むようになった。  
  
## おまけ
  
```swift
Subtracter(base: 11)(2)  // 9
```
  
構造体の実体化と callAsFunction を繋げて呼び出すことも可能。  
  
## 参考  
  
- [apple/swift-evolution: This maintains proposals for changes and user-visible enhancements to the Swift Programming Language.](https://github.com/apple/swift-evolution)  
- [swift-evolution/0253-callable.md at main · apple/swift-evolution](https://github.com/apple/swift-evolution/blob/main/proposals/0253-callable.md)  
  