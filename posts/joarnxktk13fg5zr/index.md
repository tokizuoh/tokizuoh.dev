---
title: "isBlank を String拡張で実装"
date: 2020-08-05T21:02:43+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
Swiftでも isBlank が欲しい！  
<!--more-->  
## isBlank  
自身がスペースのみで構成されていかどうかを返すプロパティ。  
様々な言語で実装されている。  
Swiftには `isEmpty` のみで、 `isBlank` は無いので作った。  
  
## 実装する機能  
- 文字列が空白だけで成り立っているかを `Bool` 型で返す
- 空文字列は false で返す  
    - 少なくともKotlinとJavaは空文字もtrueを返すが、空白のみから成り立つことの判断に使いため、falseに
  
```swift
let a = " "
print(a.isBlank)  // true
```
  
こんな感じに使いたい。  
  
## 開発環境  
  
```bash
Xcode: Version 11.5 (11E608c)  
Apple Swift version 5.2.4 (swiftlang-1103.0.32.9 clang-1103.0.32.53)
Target: x86_64-apple-darwin19.5.0
```
  
## for文  
  
```swift
extension String {
    var isBlank: Bool {
        if self.count == 0 {
            return false
        }
        let length = self.count
        var whitespaceCount = 0
        for s in self {
            if s == " " {
                whitespaceCount += 1
            } else {
                break
            }
        }
        return whitespaceCount == length
    }
}

let a = ""       // false
let b = " "      // true
let c = "a "     // false
let d = "   b"   // false
let e = "     "  // true
```
  
## filiter(_:)  
  
```swift
extension String {
    var isBlank: Bool {
        return self.count != 0 && self.count ==  self.filter { $0 == " " }.count
    }
}

let a = ""       // false
let b = " "      // true
let c = "a "     // false
let d = "   b"   // false
let e = "     "  // true
```
  
`filter(_:)` は条件に合致した要素のみ返すメソッド。  
  
## reduce(_: _:)  
  
```swift
extension String {
    var isBlank: Bool {
        return self.count != 0 && self.reduce(self.first! == " ") { (value1, value2) -> Bool in
            value1 && (value2 == " ")
        }
    }
}

let a = ""       // false
let b = " "      // true
let c = "a "     // false
let d = "   b"   // false
let e = "     "  // true
```
  
`reduce(_:_:)` はシーケンスの要素を、クロージャーを使用して一纏めにするメソッド。  
  
## trimmingCharacters(in:)  
  
```swift
import Foundation

extension String {
    var isBlank: Bool {
        return self.count != 0 && self.trimmingCharacters(in: .whitespaces).count == 0
    }
}

let a = ""       // false
let b = " "      // true
let c = "a "     // false
let d = "   b"   // false
let e = "     "  // true
```
  
`trimmingCharacters(in:)` は元の文字列から指定された `CharacterSet` に含まれる文字列を削除して返す。  
  
　
  
とりあえず4パターン書いてみた。  
まだパターンがありそう。  
  
## 参考  
  
- [isBlank - Kotlin Programming Language](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/is-blank.html)  
- [String (Java SE 11 & JDK 11 )](https://docs.oracle.com/javase/jp/11/docs/api/java.base/java/lang/String.html)  
- [filter(_:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/sequence/3018365-filter)  
- [reduce(_:_:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/array/2298686-reduce)  
- [trimmingCharacters(in:) | Apple Developer Documentation](https://developer.apple.com/documentation/foundation/nsstring/1415462-trimmingcharacters)