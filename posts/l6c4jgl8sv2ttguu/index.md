---
title: "具体例で学ぶ DispatchGroup"
date: 2020-07-29T23:22:54+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
非同期処理の流れを追いたい。  
<!--more-->  
  
## DispatchGroup  
複数の非同期処理が終わった後に処理を行う。  
  
## 例1  
  
```swift
let group = DispatchGroup()
let queue = DispatchQueue(label: "hoge")

let start = Date()
for i in 1...5 {
    group.enter()
    queue.async {
        print(i)
        sleep(5)
    }
    group.leave()
}

group.notify(queue: .main) {
    let elapsed = Date().timeIntervalSince(start)
    print("finish")
    print("elapsed: \(elapsed)")
}
```
  
```bash
1
finish
elapsed: 0.01252901554107666
2
3
4
5
```
  
`group.leave()` が非同期処理の外に置かれている。  
`print("finish")` は for文の非同期処理を除く内部処理が終わった後に実行される。（非同期処理の終了を待たない）  
  
## 例2  
  
```swift     
let group = DispatchGroup()
let queue = DispatchQueue(label: "hoge")

let start = Date()
for i in 1...5 {
    group.enter()
    queue.async {
        print(i)
        sleep(5)
        group.leave()
    }
}

group.notify(queue: .main) {
    let elapsed = Date().timeIntervalSince(start)
    print("finish")
    print("elapsed: \(elapsed)")
}
```
  
```bash
1
2
3
4
5
finish
elapsed: 25.022027015686035
```
  
直列で非同期処理を行っているので実質同期処理。  
  
## 例3  
  
```swift
let group = DispatchGroup()
let queue = DispatchQueue(label: "hoge", attributes: .concurrent)

let start = Date()
for i in 1...5 {
    group.enter()
    queue.async(group: group) {
        print(i)
        sleep(5)
        group.leave()
    }
}

group.notify(queue: .main) {
    let elapsed = Date().timeIntervalSince(start)
    print("finish")
    print("elapsed: \(elapsed)")
}
```
  
```bash
4
3
1
2
5
finish
elapsed: 5.005813002586365
```
  
`print(i)` の順序は実行毎に変わる。  
queue 生成時の関数の引数である `attribute` に `.concurrent` を指定した。  
この指定によりqueueへ追加した処理が並列で行われる。  
for文の非同期処理の終了を待ってから `print("finish")` が実行される。  
for文の並行処理の非同期処理のうち、`sleep(5)` までの処理がもっとも遅いものが終わった後に `print("finish")` が実行される。  
  
## 参考  
- [DispatchGroup | Apple Developer Documentation](https://developer.apple.com/documentation/dispatch/dispatchgroup)  