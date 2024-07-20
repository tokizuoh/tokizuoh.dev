---
title: "~= (パターンマッチング演算子) を試す"
date: 2021-10-30T14:35:22+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
使ったことがなかったので使ってみる。
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 13.0
Build version 13A233
```
  
## 前提
  
```swift
struct CustomStruct: Equatable {
    let name: String
}


var a1: [CustomStruct] = stride(from: 0, to: 1000, by: 1).compactMap {
    return .init(name: $0.description)
}

var a2: [CustomStruct] = stride(from: 0, to: 1000, by: 1).compactMap {
    return .init(name: $0.description)
}

var a3: [CustomStruct] = stride(from: 0, to: 2000, by: 2).compactMap {
    return .init(name: $0.description)
}
```
  
## == と ~= の実行結果

```swift
a1 ~= a2  // true
a1 ~= a3  // false

a1 == a2  // true
a1 == a3  // false
```
  
これだけでは違いが分からない
  
## 公式ドキュメントを読む
  
公式ドキュメントの [Equatable](https://developer.apple.com/documentation/swift/1539154) に記載があったがよく分からなかったので、range 配下の記載を読む。  
  
> Returns a Boolean value indicating whether a value is included in a range.  
> [~=(_:_:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/range/2428743)
  
「値が範囲に含まれるかどうかを示す Boolean を返します。」  
rangeを使って書いてみる。  
  
## range で試す
  
```swift
let hoge = 0..<10

hoge ~= 1   // true
hoge ~= 10  // false
```
  
　
  
```swift
let hoge = 0..<10

hoge ~= 1   // true
hoge ~= 10  // false

hoge.contains(1)   // true
hoge.contains(10)  // false
```
  
contains で担保できる気がする…？  
  
## contains との棲み分け
  
パターンマッチング演算子 にも存在意義がある。  
Equatable 配下の記載を読むと、  
  
> In most cases, you should use the equal-to operator (==) to test whether two instances are equal. The pattern-matching operator is primarily intended to enable case statement pattern matching.  
> [~=(_:_:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/1539154)  
  
「~= 演算子は case 文を有効にすることを目的としている」とのこと。  
  
　
  
```swift
let hoge = 0..<10

if case hoge = 1 {
    print("match!")  // match!
} else {
    print("not match...")
}
```
  
if case で範囲に含まれていることを確認できることが分かった。  
  
自分は contains の方が理解しやすいと思うので今の所使う予定は無いが、知っておくのは良いだろう。  
  
## 参考  
  
- [~=(_:_:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/1539154)  
- [~=(_:_:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/range/2428743)  
  