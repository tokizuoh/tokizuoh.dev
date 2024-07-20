---
title: "Kotlin で解く AtCoder Beginner Contest 063"
date: 2021-10-23T00:59:41+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/kotlin.png"
tags:
- kotlin
---
  
KMM (Kotlin Multiplatform Mobile) の導入のために,まず Kotlin に入門してみる。  
  
<!--more-->  
  
[AtCoder Beginner Contest 063](https://atcoder.jp/contests/abc063) を解く。  
  
## 開発環境  
  
```bash
Gradle: org.jetbrains.kotlin:kotlin-stdlib:1.5.10
```
  
## [A - Restricted](https://atcoder.jp/contests/abc063/tasks/abc063_a)  
  
- 複数空白区切り入力（Int）
  - [readLine](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/read-line.html) の戻り値は nullable (SwiftのOptional型に似てる概念?) なので non-null にする
  
　
```kt
fun main() {
    val (a, b) = readLine()!!.split(" ").map{it.toInt()}
    val answer = a + b

    if (answer >= 10) {
        println("error")
    } else {
        println(answer)
    }
}
```
  
## [B - Varied](https://atcoder.jp/contests/abc063/tasks/abc063_b)  
  
- 単一入力(String)
- 重複検出
  
　
```kt
fun main() {
    val s = readLine()!!
    val sortedSet = s.toSortedSet()

    if (s.length == sortedSet.size) {
        println("yes")
    } else {
        println("no")
    }
}
```
  
## [C - Bugged](https://atcoder.jp/contests/abc063/tasks/arc075_a)  
  
- 複数行入力（int）
- 剰余
- 関数
　
  
　
```kt
fun canDivide(num: Int, by: Int): Boolean {
    return num % by == 0
}

fun main() {
    val n = readLine()!!.toInt()
    val nums = (0 until n).map { readLine()!!.toInt() }.sorted()
    
    val sum = nums.sum()

    if (!canDivide(sum, 10)) {
        println(sum)
        return
    }

    nums.forEach{ num ->
        val remained = sum - num
        if (!canDivide(remained, 10)) {
            println(remained)
            return
        }
    }

    println(0)
}
```
  
## [D - Widespread](https://atcoder.jp/contests/abc063/tasks/arc075_b)  
  
- 関数
  
　
```kt
fun canBeatAll(num: Long, a: Long, b: Long, H: List<Long>): Boolean {
    var total: Long = 0
    H.forEach { h ->
        if (h - b * num > 0) {
            total += (h - b * num + a - b - 1) / (a - b)
        }
    }
    return total > num
}

fun main() {
    val (n, a, b) = readLine()!!.split(" ").map{it.toLong()}
    val H = (0 until n).map { readLine()!!.toLong() }

    var ng: Long = 0
    var ok: Long = 1000000010
    while (ok - ng > 1) {
        val mid = (ng + ok) / 2

        if (canBeatAll(mid, a, b, H)) {
            ng = mid
        } else {
            ok = mid
        }
    }

    println(ok)
}
```
  
## 感想
  
- 組み込み関数が強力（[toSortedSet](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-sorted-set.html)）  
- nullale 関係の仕組みがあって良い (色々なとこでゼロ値だけではキツいところがある)
- var, val 分かりにくい
- Swift に慣れたからか、if文の条件式の括弧書くのが手間
  
## 参考
  
- [readLine - Kotlin Programming Language](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/read-line.html)  
- [toSortedSet - Kotlin Programming Language](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-sorted-set.html)  
  