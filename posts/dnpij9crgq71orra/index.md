---
title: "abc (抽象基底クラス) を試す"
date: 2021-09-26T16:43:31+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/python.png"
tags:
- python
---
  
コード書いてる時に予測変換で「abc」が出て、知らなかったので調べた。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> python -V
Python 3.10.0rc2
```
  
## abc (抽象基底クラス)  とは
  
abc は abstract base class の頭文字で、抽象基底クラスを表す。  
インスタンスとしてではなく、そのもの自体を継承させることを前提としたクラス。  
  
Swift の [Protocol](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html) のようなイメージ。  
  
## abc を直接インスタンス化
  
不可能。継承させることを前提としたクラスなので、abc 自体をインスタンス化できない。  
  
```python
import abc


class Bird(metaclass=abc.ABCMeta):

    @abc.abstractclassmethod
    def fly(self):
        pass


def main():
    bird = Bird()


if __name__ == "__main__":
    main()
```
  
　
  
```bash
> python main.py
Traceback (most recent call last):
  File "/py/src/main.py", line 16, in <module>
    main()
  File "/py/src/main.py", line 12, in main
    bird = Bird()
TypeError: Can't instantiate abstract class Bird with abstract method fly
```
  
## abc を継承したクラスを作成
  
```python
import abc


class Bird(metaclass=abc.ABCMeta):

    @abc.abstractclassmethod
    def fly(self):
        print("fly")


class Sparrow(Bird):
    
    def fly(self):
        return super().fly()


def main():
    bird = Sparrow()
    bird.fly()  # "fly"


if __name__ == "__main__":
    main()
```
  
## abc 判定
  
```python
import inspect

def main():
    print(inspect.isabstract(Bird))  # True
```
  
## サブクラス判定
  
```python
def main():
    sparrow = Sparrow()
    parrot = Parrot()

    print(issubclass(Sparrow, Bird))  # True
```
  
abc はクラスなので、クラスとしての判定も機能する。  
  

  
## 参考  
  
- [Protocols — The Swift Programming Language (Swift 5.5)](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)  
- [abc — Abstract Base Classes — Python 3.9.7 documentation](https://docs.python.org/3/library/abc.html)  
- [inspect --- 活動中のオブジェクトの情報を取得する — Python 3.9.4 ドキュメント](https://docs.python.org/ja/3/library/inspect.html)  
  