---
title: "compactMap"
date: 2020-07-28T23:03:14+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
nil許容非許容。  
<!--more-->  
  
## compactMap  
```swift
let items = [1, 2, 3, 4, 5]

let newItems = items.compactMap { (item) in
    return item * 2
}

print(newItems)  // [2, 4, 6, 8, 10]
```
  
Sequence に準拠している型の実体の、各要素に対して変換処理を加える。  
  
　
  
```swift
// コンパイルエラー: Value of optional type 'Int?' must be unwrapped to a value of type 'Int'
let items = [1, 2, 3, 4, 5, nil]

let newItems = items.compactMap { (item) in
    return item * 2
}

print(newItems)
```
  
compactMapはOptional型に対してのハンドリングが必要。  
  
## compactMapでindexを取得する  
compactMapで要素番号を得るために `enumerated()` を使用。
  
まず、nil非存在の配列で実行。  
  
```swift
let items = [1, 2, 3, 4, 5]

let newItems = items.enumerated().compactMap { (item) in
    return item.offset
}

print(newItems)  // [0, 1, 2, 3, 4]
```
  
　
  
次に、nil存在の配列で実行。  
  
```swift
let items = [1, 2, 3, 4, 5, nil, 1]

let newItems = items.enumerated().compactMap { (item) in
    return item.offset
}

print(newItems)  // [0, 1, 2, 3, 4, 5, 6]
```
  
nilの要素番号も取得している。  
  
　
  
```swift
let items = [1, 2, 3, 4, 5, nil, 1]

let newItems = items.enumerated().compactMap { (item) in
    return item
}

print(type(of: newItems))  // Array<(offset: Int, element: Optional<Int>)>
```
  
配列に `enumerated()` を適用した段階でnil許容となるため、nilの要素のindexも取得している。  
  
## compactMapでindexを取得する （nilの要素以外）  
  
```swift
let items = [1, 2, 3, 4, 5, nil, 1]

let newItems = items.enumerated().filter { (item) -> Bool in
    return item.element != nil
}

let newItemsIndices = newItems.compactMap { (newItem) in
    return newItem.offset
}

print(newItemsIndices)  // [0, 1, 2, 3, 4, 6]
```
  
compactMapの実行前に `filter` でnilを除去している。  
これより短い書き方を探す。  
  
## 参考  
- [compactMap(_:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/sequence/2950916-compactmap)  
- [enumerated() | Apple Developer Documentation](https://developer.apple.com/documentation/swift/array/1687832-enumerated)  