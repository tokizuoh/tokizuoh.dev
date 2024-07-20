---
title: "MementoパターンをSwiftで書く"
date: 2021-11-18T23:58:51+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
知らない概念はすぐ書く！  
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version[master]
Xcode 13.1
Build version 13A1030d
```
  
## はじめに
本記事は [18．Memento パターン | TECHSCORE(テックスコア)](https://www.techscore.com/tech/DesignPattern/Memento.html/) に沿って、MementoパターンをSwiftで書いて学んでいく。
  
## Mementoパターン とは
  
GoFのデザインパターンの一つ。  
インスタンスのあるときの状態をスナップショットとして保存するパターン。  
  
再利用できる箇所は再利用しよう、という考え。  
[Flyweightパターン](https://tokizuoh.dev/posts/rquscz3fu26v7ndq/) と似ているが、棲み分けはこんな感じだと思う。  
  
- Memento: インスタンスのあるときの状態を保存し、計算結果など重複する箇所は再利用しよう
- Flyweight: インスタンスそのものを再利用してリソースを無駄なく使おう
  
## お題
  
1. 1から5の総和を求める
2. 1から10の総和を求める
  
## パターンを考えずに実装
  
```swift
import Foundation

final class Calculator {
    func calculate(_ addends: [Int]) -> Int {
        return addends.reduce(0) { $0 + $1 }
    }
}

let addends1: [Int] = [1, 2, 3, 4, 5]
let total1 = Calculator().calculate(addends1)  // 15

let addends2: [Int] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let total2 = Calculator().calculate(addends2)  // 55
```
  
1から5の総和が繰り返し行われているので効率化できる。  
ここでMementパターンの出番。  
  
## Mementパターンを適用して実装
  
```swift
import Foundation

final class Calculator {
    private var tmp: Int = 0

    func plus(_ addend: Int) {
        tmp += addend
    }

    func createMemento() -> Memento {
        return .init(result: tmp)
    }

    func setMement(_ memento: Memento) {
        tmp = memento.getResult()
    }

    func getTmp() -> Int {
        return tmp
    }
}

final class Memento {
    private var result: Int? = nil

    init(result: Int) {
        self.result = result
    }

    func getResult() -> Int {
        return result ?? 0
    }
}

let calculator1 = Calculator()
let addends1: [Int] = [1, 2, 3, 4, 5]
addends1.forEach { addend in
    calculator1.plus(addend)
}
print("5までの足し算", calculator1.getTmp())  // 15

let memento = calculator1.createMemento()

let calculator2 = Calculator()
calculator2.setMement(memento)
let addends2: [Int] = [6, 7, 8, 9, 10]
addends2.forEach { addend in
    calculator2.plus(addend)
}

print("10までの足し算", calculator2.getTmp())  // 55
```
  
1から5の総和 を再利用できたので効率化できた。  
  
## 参考  
  
- [18．Memento パターン | TECHSCORE(テックスコア)](https://www.techscore.com/tech/DesignPattern/Memento.html/)  
  