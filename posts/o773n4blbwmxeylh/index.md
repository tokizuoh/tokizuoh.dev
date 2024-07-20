---
title: "Swift の rethrows を試す"
date: 2021-07-31T09:33:47+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
意識的に使ったことが無かったので使ってみた。  
  
<!--more-->

## 開発環境
  
```bash
> xcodebuild -version 
Xcode 12.3
Build version 12C33
```
  
## rethrows
  
例外を throw するクロージャを引数に取る場合、関数の定義に rethrows を定義する。  
[removeAll(where:)](https://developer.apple.com/documentation/swift/array/3017530-removeall) や [compactMap(_:)](https://developer.apple.com/documentation/swift/sequence/2950916-compactmap) に使用されている。  
  
## 使用例  
  
`compactMap(_:)` で rethrows を体験する。  
  
　
  
```swift
import Foundation

struct OddError: Error {}

let a = [0, 0, 1 ,0, 0]

let b = try! a.compactMap { num -> Int in
    if num % 2 == 1 {
        throw OddError()
    }
    return num * 2
}

print(b)
```
  
↓
  
```bash
Fatal error: 'try!' expression unexpectedly raised an error: __lldb_expr_1.OddError():
```
  
---
  
`try!` を `try?` にするとエラーが潰され、`b` には `nil` が入る。  
  
---
  
```swift
import Foundation

struct OddError: Error {}

let a = [0, 0, 1 ,0, 0]

do {
    try a.compactMap { num -> Int in
        if num % 2 == 1 {
            throw OddError()
        }
        return num * 2
    }
} catch is OddError {
    print("奇数が入ってる！")
}
```
  
↓
  
```bash
奇数が入ってる！
```
  
　
  
`compactMap(_:)` が引数に持つクロージャを実行し、そのクロージャ内で例外を throw するときに、実行側は`compactMap(_:)` 自体が throw した風に見える。  
throw するのが直下でない場合のエラーハンドリングをシンプルに書けることがメリットに感じた。  
  
## 参考
  
- [removeAll(where:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/array/3017530-removeall)  
- [compactMap(_:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/sequence/2950916-compactmap)  
  
