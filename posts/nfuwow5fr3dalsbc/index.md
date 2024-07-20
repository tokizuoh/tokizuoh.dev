---
title: "swift-algorithms Chunkedを試す"
date: 2020-10-10T21:09:43+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
- algorithms
---
  
[apple/swift-algorithms](https://github.com/apple/swift-algorithms)  
appleからswiftのアルゴリズムライブラリが発表された。  
  
<!--more-->  
  
## swift-algorithmsとは  
sequenceやcollectionアルゴリズムのopen-source package。  
2020/10/10 現在下記が実装されている。  
  
- Chain
- Chunked
- Combinations
- Cycle
- Indexed
- Partition
- Permutations
- Product
- RandomSample
- Rotate
- Unique
  
今回はChunkedを試してみる。  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 12.0.1
Build version 12A7300

> swift --version
Apple Swift version 5.3 (swiftlang-1200.0.29.2 clang-1200.0.30.1)
Target: x86_64-apple-darwin19.6.0
```
  
## Chunked  
collectionをsubsequenceに分割する。  
`chunked(by:)` と `chunked(on:)` の2種類定義されている。  
  
　
  
#### chunked(by:)
  
```swift
import Algorithms

private func main() {
    let numbers = [10, 20, 30, 10, 40, 40, 10, 20]
    let chunks = numbers.chunked(by: {$0 >= $1})
    print(chunks)
}

main()
// [ArraySlice([10]), ArraySlice([20]), ArraySlice([30, 10]), ArraySlice([40, 40, 10]), ArraySlice([20])]
```
  
byに渡した条件が `false` の際に、chunk（塊）を分割する。
  
　
  
#### chunked(on:)
  
```swift
import Algorithms

private func main() {
    let names = ["hoge", "human", "ratio", "piyo", "gusto"]
    let chunks = names.chunked(on: \.last)
    print(chunks)
}

main()
// [ArraySlice(["hoge"]), ArraySlice(["human"]), ArraySlice(["ratio", "piyo", "gusto"])]
```
  
各要素の射影を取り、2つの連続する要素の射影が一致しない際に、chunkを分割する。  
  
　
  
```swift
import Algorithms

private func main() {
    let names = ["ratio", "hoge", "human", "ratio", "piyo", "gusto"]
    let chunks = names.chunked(on: \.last)
    print(chunks)
}

main()
// [ArraySlice(["ratio"]), ArraySlice(["hoge"]), ArraySlice(["human"]), ArraySlice(["ratio", "piyo", "gusto"])]
```
  
連続する2つの要素に対する処理なので、同じ値でも位置によって分割後のchunkが異なる。  
  
## 参考  
- [apple/swift-algorithms: Swift Algorithms is an open-source package of sequence and collection algorithms, along with their related types.](https://github.com/apple/swift-algorithms)