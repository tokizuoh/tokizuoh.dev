---
title: "Swift の if-let の Kotlin版"
date: 2021-10-27T00:36:26+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/kotlin.png"
tags:
- kotlin
---
  
頭の中でSwiftとの対応表を作りたい。  
  
<!--more-->  
  
## 開発環境  
  
[Kotlin Playground](https://play.kotlinlang.org/#eyJ2ZXJzaW9uIjoiMS41LjMxIiwicGxhdGZvcm0iOiJqYXZhIiwiYXJncyI6IiIsIm5vbmVNYXJrZXJzIjp0cnVlLCJ0aGVtZSI6ImlkZWEiLCJjb2RlIjoiLyoqXG4gKiBZb3UgY2FuIGVkaXQsIHJ1biwgYW5kIHNoYXJlIHRoaXMgY29kZS4gXG4gKiBwbGF5LmtvdGxpbmxhbmcub3JnIFxuICovXG5cbmZ1biBtYWluKCkge1xuICAgIHByaW50bG4oXCJIZWxsbywgd29ybGQhISFcIilcbn0ifQ==)  
  
```kotlin
fun main() {
	println(KotlinVersion.CURRENT)  // 1.5.31
}
```
  
## Swift の if-let
  
```swift
let hoge: Int? = 1

if let hoge = hoge {
  print(hoge)  // 1
}
```
  
Optional な値に対して実体がある場合に処理を行う時に書く。  
  
## Kotlin
  
```kotlin/
fun main() {
    val hoge: Int? = 1
    
    hoge?.let {
        print(hoge) // 1
    }
}
```
  
　
  
if-let の else は Kotlin では以下のように書くっぽい。  
  
```kotlin
fun main() {
    val hoge: Int? = null
    
    hoge?.let {
        print(hoge)
    } ?: run {
        print("null!")  // null!
    }
}
```
  
## run
  
[run - Kotlin Programming Language](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/run.html)  
  
「指定された関数ブロックを呼び出し、その結果を返す」
  
スコープ関数の一種。let もスコープ関数だったので使ってみる。  
  
## let
  
[Scope functions | Kotlin](https://kotlinlang.org/docs/scope-functions.html)  
  
とりあえず書く。  
  
```kotlin
fun main() {
    val hoge = 10
    
    
    val fuga = hoge.let {
        100*100
    }
    
    println(fuga)  // 10000
}
```
  
　
  
```kotlin
fun main() {
    val hoge = 10
    
    
    val fuga = hoge.let {
        it*100
    }
    
    println(fuga) // 1000
}
```
  
式として利用できる。  
  
## run
  
```kotlin
fun main() {
    val hoge = 10
    
    
    val fuga = hoge.run{
        100*100
    }
    
    println(fuga)  // 10000
}
```
  
　
  
```kotlin
fun main() {
    val hoge = 10
    
    
    val fuga = hoge.run {
        this*100
    }
    
    println(fuga) // 1000
}
```
  
let と run では Object reference が it と this で分かれている。  
次回以降スコープ関数についてもう少し見る。  
  
脱線したが、Kotlin で Swift の if-let をやりたい時は、以下のように書く。  
  
```kotlin
fun main() {
    val hoge: Int? = null
    
    hoge?.let {
        print(hoge)
    } ?: run {
        print("null!")  // null!
    }
}
```
  
## 参考  
  
- [run - Kotlin Programming Language](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/run.html)  
- [Scope functions | Kotlin](https://kotlinlang.org/docs/scope-functions.html)  
  