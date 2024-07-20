---
title: "FlyweightパターンをSwiftで学ぶ"
date: 2021-11-06T16:20:51+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
知らない概念はすぐ書く！  
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 13.1
Build version 13A1030d
```
  
## はじめに  
  
本記事は [20．Flyweight パターン | TECHSCORE(テックスコア)](https://www.techscore.com/tech/DesignPattern/Flyweight.html/) に沿って、FlyweightパターンをSwiftで書いて学んでいく。  
  
## Flyweightパターン とは
  
GoFのデザインパターンの一つ。  
インスタンスを共有することでリソースを無駄なく使うことに焦点を当てたパターン。  
  
使い回せるところは積極的に使いまわそう、という考え。  
  
## サンプルコード
  
題材として、人文字を使って複数の人間でメッセージを生成し、メッセージごとに写真を取ることを考える。  
  
今回は `"success"` を例に進める。  
  
```swift
import Foundation

final class HumanLetter {
    let letter: String
    init(_ letter: String) {
        self.letter = letter
    }
    func getLetter() -> String {
        return letter
    }
}

private func takeAPhoto(humanLetter: HumanLetter) {
    print(humanLetter.getLetter())
}


// success
let s = HumanLetter("s")
takeAPhoto(humanLetter: s)
let u = HumanLetter("u")
takeAPhoto(humanLetter: u)
let c = HumanLetter("c")
takeAPhoto(humanLetter: c)
let c2 = HumanLetter("c")
takeAPhoto(humanLetter: c2)
let e = HumanLetter("e")
takeAPhoto(humanLetter: e)
let s2 = HumanLetter("s")
takeAPhoto(humanLetter: s2)
let s3 = HumanLetter("s")
takeAPhoto(humanLetter: s3)
```
  
`"success"`は`"s"`が3回、`"c"`が2回重複している。  
重複しているインスタンスは使い回せば良いのでは？、というのが今回の主題。  
  
　
  
```swift
// success
let s = HumanLetter("s")
takeAPhoto(humanLetter: s)
let u = HumanLetter("u")
takeAPhoto(humanLetter: u)
let c = HumanLetter("c")
takeAPhoto(humanLetter: c)
takeAPhoto(humanLetter: c)
let e = HumanLetter("e")
takeAPhoto(humanLetter: e)
takeAPhoto(humanLetter: s)
takeAPhoto(humanLetter: s)
```
  
これでインスタンスを使い回すことが出来たが、これでは利用側が任意の人文字に対して、インスタンスが生成されているか知っている必要がある。  
  
　
  
```swift
import Foundation

final class HumanLetter {
    let letter: String
    init(_ letter: String) {
        self.letter = letter
    }
    func getLetter() -> String {
        return letter
    }
}

private func takeAPhoto(humanLetter: HumanLetter) {
    print(humanLetter.getLetter())
}

final class HumanLetterFactory {
    private init() {}
    
    static let shared = HumanLetterFactory()
    
    var dict: [String: HumanLetter] = [:]
    
    func getHumanLetter(letter: String) -> HumanLetter {
        if let humanLetter = dict[letter] {
            return humanLetter
        } else {
            dict.updateValue(HumanLetter(letter),
                             forKey: letter)
            return dict[letter]!
        }
    }
}

let factory = HumanLetterFactory.shared

let letters = "success"

letters.forEach { character in
    let letter = String(character)
    let humanLetter = factory.getHumanLetter(letter: letter)
    takeAPhoto(humanLetter: humanLetter)
}
```
  
上記が最終的なFlyweightパターンに沿った実装となる。  
  
　
  
メリットとして、  
  
- 利用側がインスタンスの二重生成を気にしなくて済む
- 利用側がインスタンスが既に生成されているかを気にせずに呼び出せる  
  
## まとめ
  
Flyweightパターンを利用することで、  
  
- 利用側が気にすることが減る (インスタンスの二重生成、既に生成されているか)
- インスタンスを使い回すのでリソースを無駄遣いせずに済む
  
## 参考  
  
- [20．Flyweight パターン | TECHSCORE(テックスコア)](https://www.techscore.com/tech/DesignPattern/Flyweight.html/)  
  