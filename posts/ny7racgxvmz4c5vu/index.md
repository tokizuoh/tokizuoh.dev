---
title: "（供養）クロージャーの評価遅延"
date: 2020-08-04T22:35:55+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
  
何もせずとも遅延処理になってしまいautoclosureの必要性が分かっていない。  
  
<!-- 関数の引数に処理が重いクロージャが渡される場合に役立つ。-->  
<!--more-->  
  
## はじめに
この記事は供養記事です。  
筆者が思い浮かべていた動作と異なる処理結果となりました。  
  
## 開発環境  
  
```bash
Xcode: Version 11.5 (11E608c)  
Apple Swift version 5.2.4 (swiftlang-1103.0.32.9 clang-1103.0.32.53)
Target: x86_64-apple-darwin19.5.0
```
  
## @autoclosure  
  
関数の引数として `@autoclosure` を付与されたクロージャーは、呼び出されるまで実行されない。  
評価を遅延させることができる。  
  
## 具体例  
  
```swift
func fuga(flag: Bool, closure: () -> ()) {
    if !flag {
        return
    }
    closure()
}
```
  
`flag` が true の時に引数のクロージャーを実行する関数。  
  
　
  
```swift
struct Hoge {
    static var number = 1
}

let repeat200000: () -> () = {
    var total = 0
    for i in 0 ... 200000 {
        total += 1
    }
    Hoge.number = total
}
```
  
200000 回インクリメントした変数を構造体のstaticプロパティに格納するクロージャー。  
  
　
  
```swift
fuga(flag: true, closure: repeat200000)  // elapsed: 1.9918900728225708
fuga(flag: false, closure: repeat200000) // elapsed: 0.01321399211883545
```
  
本来はtrue/false関わらず同等の時間がかかってほしかったが、結果は異なった。  
`@autoclosure` を使用することで遅延評価が行われ、実行速度に差異が出ることを期待していた。。。  
宿題にする。  
  
## 参考  
  
- [Closures — The Swift Programming Language (Swift 5.3)](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)