---
title: "private(set)"
date: 2020-07-24T22:11:29+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
プロパティ勝手にいじらんといてや！！  
<!--more-->  
  
## private(set)  
参照可、変更不可な修飾子。  
  
## コードで理解  
  
Swift 5.2  
  
```swift
  
class Bird {
    var wingLength: Int
    
    init(wingLength: Int) {
        self.wingLength = wingLength
    
    }
    
    func glow() {
        wingLength += 10
    }
}
```
  
　
  
```swift
let hawk = Bird(wingLength: 51)

hawk.glow()
print(hawk.wingLength)  // 61

hawk.wingLength += 23
print(hawk.wingLength)  // 84
```
インスタンスの `wingLength(Int)` を増やしたい場合は、`glow()` か、直接値を触る必要がある。  
  
　
  
ここで、 `wingLength(Int)` を 直接触らせたくない場合に `private(set)` が有効なことを示す。  
`wingLength(Int)` を `private(set)` での宣言に変更する。  
  
```swift
class Bird {
    private(set) var wingLength: Int  // 変更

    init(wingLength: Int) {
        self.wingLength = wingLength

    }

    func glow() {
        wingLength += 10
    }
}
```
  
　
  
```swift
let hawk = Bird(wingLength: 51)

hawk.glow()
print(hawk.wingLength)  // 61
```
`wingLength(Int)` を直接触っていないので実行できる。  
  
　
  
```swift
// コンパイルエラー
// error: left side of mutating operator isn't mutable: 'wingLength' setter is inaccessible
let hawk = Bird(wingLength: 51)

hawk.wingLength += 100
print(hawk.wingLength) 
```
`wingLength(Int)` を直接触っており、アクセス権が無いのでコンパイルエラー。  
  
