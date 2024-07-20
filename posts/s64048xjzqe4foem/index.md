---
title: "抽象クラスと interface の違いを見る"
date: 2021-10-25T20:31:25+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/kotlin.png"
tags:
- kotlin
---
  
※ 本記事は感想100%です。  
  
<!--more-->  
  
## 開発環境  
  
```bash
Gradle: org.jetbrains.kotlin:kotlin-stdlib:1.5.10
```
  
## 抽象クラス
  
```kotlin
abstract class Animal {
    abstract val name: String
    abstract fun bark()
}

class Dog(override val name: String) : Animal() {
    override fun bark() {
        println("AO~N")
    }
}

fun main() {
    val dog = Dog(name = "INU")
    dog.bark()  // AO~N
}
```
  
## interface
  
```kotlin
interface Animal {
    val name: String
    fun bark()
}

class Dog(override val name: String) : Animal {
    override fun bark() {
        println("AO~N")
    }
}

fun main() {
    val dog = Dog(name = "INU")
    dog.bark()  // AO~N
}
```
  
　
  
違いが分からない。  
明確な棲み分けはどうなるんだ？  
色々試してみる。  
  
どちらもエラーが出ると思うが、具象化したクラスを作る前に実体化を試みる。  
  
## そのまま実体化: 抽象クラス (できない)
  
エラー。  
  
```kotlin
abstract class Animal {
    abstract val name: String
    abstract fun bark()
}

class Dog(override val name: String) : Animal() {
    override fun bark() {
        println("AO~N")
    }
}

fun main() {
    // Cannot create an instance of an abstract class
    val animal = Animal(name = "hoge")
    dog.bark()  // AO~N
}
```
  
「抽象クラスのインスタンスを作成できません」  
そういう仕組みなのだろう。  
  
## そのまま実体化: interface (できない)
  
エラー。  
  
```kotlin
interface Animal {
    val name: String
    fun bark()
}

class Dog(override val name: String) : Animal {
    override fun bark() {
        println("AO~N")
    }
}

fun main() {
    // Interface Animal does not have constructors
    val animal = Animal(name = "hoge")
    animal.bark()
}
```
  
「Interface Animalにはコンストラクタがありません」  
interface はクラスではない → クラスでないのでコンストラクタがない、という理解。  
  
　
  
もう少し試す。  
  
## 複数具象: 抽象クラス (できない)
  
エラー。  
  
```kotlin
abstract class Animal {
    abstract val name: String
    abstract fun bark()
}

abstract class Plant {
    abstract var hitPoint: Int
    abstract fun grow(water: Int)
}

// Only one class may appear in a supertype list
class DogPlant(override val name: String, override var hitPoint: Int) : Animal(), Plant() {
    override fun bark() {
        println("AO~N")
    }

    override fun grow(water: Int) {
        hitPoint += water
    }
}

fun main() {
    val dog = DogPlant(name = "INU_FLOWER", hitPoint = 100)
    dog.bark()
}
```
  
そもそも複数継承というよりかは、複数具象が適切な日本語かもしれない。  
抽象クラスの具象化に際し、複数の抽象クラスからは具象化できない。  
  
## 複数具象: interface 
  
```kotlin
interface Animal {
    val name: String
    fun bark()
}

interface Plant {
    var hitPoint: Int
    fun grow(water: Int)
}

class DogPlant(override val name: String, override var hitPoint: Int) : Animal, Plant {
    override fun bark() {
        println("AO~N")
    }

    override fun grow(water: Int) {
        hitPoint += water
    }

    fun showHitPoint() {
        println("%s's HitPoint: %d".format(name, hitPoint))
    }
}

fun main() {
    val dogPlant = DogPlant(name = "INU_FLOWER", hitPoint = 100)
    dogPlant.grow(15)
    dogPlant.showHitPoint()  // INU_FLOWER's HitPoint: 115
}
```
  
こっちはできた。  
  
## まとめ
  
|  | 抽象クラス | interface |
| --- | --- | --- |
| 単一具象 | o | o |
| そのまま実体化 | x | x |
| 複数具象 | x | o |
  
　
  
ここまでの理解で、複数ある抽象を具象化させるためには interface を使う必要がある、というイメージに落ち着いた。  
  
Swift (iOS) で例えると、「この ViewController には共通の ViewController のクラスを継承させたい。また、MVPを採用しているから Presenter の Output の protocol を ViewController に準拠させたい」という場合は、ViewController に対して2つのinterface を具象化させる必要がある？ もう少し見てみる。  
  