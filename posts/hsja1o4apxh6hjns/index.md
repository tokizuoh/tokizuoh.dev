---
title: "Combine を試す (Just, Future)"
date: 2021-10-12T23:26:58+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 13.0
Build version 13A233
```
  
## Combine とは
  
[Combine | Apple Developer Documentation](https://developer.apple.com/documentation/combine)  
  
宣言型なAPIを提供するフレームワーク。  
  
iOS13から利用可能。  
複数のコールバック, クロージャによる値伝搬をせずに処理を書くことができる。  
  
　
  
基本的な登場人物（概念）は2つ。  
  
- Publisher: 値を配信する役割。
- Subscriber: Publisherから受け取った要素に基づき動作する役割。  
  
## シンプルな例
  
```swift
import Combine

let publisher = (0..<10).publisher  // Publisher (Publishers.Sequence) を生成
    .filter {  // Publisher (Publishers.Filter) を生成
        $0 % 2 == 1
    }

let subscriber = publisher.sink { num in  // Subscriber (Subscribers.Sink) を生成
    print(num)  // [1, 3, 5, 7, 9] が順に入る
}
```
  
　
  
基本的な処理の流れは以下。  
  
1. Publisherを生成（複数可）  
2. Subscriverで値を受け取って処理  
  
## Just
  
[Just | Apple Developer Documentation](https://developer.apple.com/documentation/combine/just)
  
Combineに組み込みで定義されているPublisher。  
各Subscriberに1回だけ出力を送信し、その後終了するPublisher。  
  
　
  
```swift
import Combine

let publisher = Just("Hello, World!")

publisher.sink { str in
    print("received:", str)  // `received: Hello, World!`
}
```
  
Publisherが値を配信する際に、処理結果に応じてエラーハンドリングをしたいときは [Future]() を使う。  
  
## Future
  
[Future | Apple Developer Documentation](https://developer.apple.com/documentation/combine/future)  
  
Combineに組み込みで定義されているPublisher。  
最終的に単一の値を生成し、その後終了または失敗するPublisher。
  
#### 成功
  
```swift
import Combine

enum CustomError: Error {
    case domain
}

let publisher = Future<String, CustomError> { promise in
    promise(.success("hoge"))
}
    
publisher.sink { completion in
    switch completion {
    case .finished:
        print("Finished!")
        
    case .failure(let error):
        print("Failure:", error)
    }
    
} receiveValue: { value in
    print("received:", value)
}

// received: hoge
// Finished!
```
  
#### 失敗
  
```swift
import Combine

enum CustomError: Error {
    case domain
}

let publisher = Future<String, CustomError> { promise in
    promise(.failure(.domain))  // 変更
}
    
publisher.sink { completion in
    switch completion {
    case .finished:
        print("Finished!")
        
    case .failure(let error):
        print("Failure:", error)
    }
    
} receiveValue: { value in
    print("received:", value)
}

// Failure: domain
```
  
失敗時は 値の受信時に実行するクロージャ（`receiveValue`）が実行されずに終了する。  
  
## Just と Future
  
```swift
import Combine

enum CustomError: Error {
    case domain
}

let publisher = Future<String, CustomError> { promise in
    promise(.failure(.domain))
}.catch { error in
    Just("エラー出てるぞ！！！")  // ここ
}

publisher.sink { completion in
    switch completion {
    case .finished:
        print("Finished!")

    case .failure(let error):
        print("Failure:", error)
    }
} receiveValue: { value in
    print("received:", value)
}

// received: エラー出てるぞ！！！
// Finished!
```
  
Futureで失敗した時にJustを使うことで代替の値を配信することができる。  
その場合、Subscriber側では成功扱いとなり、Justで配信した値を受け取る。  
  
## 参考  
  
- [Combine | Apple Developer Documentation](https://developer.apple.com/documentation/combine)  
- [Just | Apple Developer Documentation](https://developer.apple.com/documentation/combine/just)  
- [Future | Apple Developer Documentation](https://developer.apple.com/documentation/combine/future)  
  