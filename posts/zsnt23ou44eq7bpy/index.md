---
title: "Python3.9を試す"
date: 2020-10-10T12:11:34+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/docker_python.png"
tags:
- docker
- python
---
  
こういうときDocker便利だ。  
<!--more-->  
  
## 開発環境  
  
Dockerで **python:3.9-alpine** のimageを使用。  
  
```bash
> docker --version
Docker version 19.03.12, build 48a66213fe

/ # python -V
Python 3.9.0
```
  
## Add Union Operators To dict ([PEP 584](https://www.python.org/dev/peps/pep-0584/))  
dictのUnion演算子の追加。  
  
```python
d1 = {"hoge": 1}
d2 = {"fuga": 2, "hoge": 3}

d3 = d1 | d2  # {'hoge': 3, 'fuga': 2}
```
  
キーが重複した場合は、最後に評価された値が格納される。  
  
## Type Hinting Generics In Standard Collections ([PEP 585](https://www.python.org/dev/peps/pep-0585/))  
標準Collectionの型ヒントの追加。  
  
```python
a: list[int] = [1, 2]
```
  
　
  
> This design means that it is possible to create instances of parameterized collections
  
```python
a = list[int]()
a.append("2")  # もちろんint以外の型の変数もappend可能
```
  
## Expand math.gcd() and math.lcm() to accept multiple arguments  
  
#### math.gcd()
  
> Expanded the math.gcd() function to handle multiple arguments. Formerly, it only supported two arguments.  
> (from [What’s New In Python 3.9 — Python 3.9.0 documentation](https://docs.python.org/3.9/whatsnew/3.9.html#math))
  
gcdの引数が3つ以上指定可能になった。
  
```python
import math
g = math.gcd(4, 8, 12)  # 4
```
  
　
  
#### math.lcm()
  
> Added math.lcm(): return the least common multiple of specified arguments.  
> (from [What’s New In Python 3.9 — Python 3.9.0 documentation](https://docs.python.org/3.9/whatsnew/3.9.html#math))  
  
lcmが使えるようになった。lcmの引数も3つ以上いけるっぽい。  
  
```python
import math
math.lcm(3, 4)  # 12
math.lcm(3, 4, 5, 7)  # 420
```
  
## 参考  
- [python - Docker Hub](https://hub.docker.com/_/python)  
- [Python Release Python 3.9.0 | Python.org](https://www.python.org/downloads/release/python-390/)  
- [What’s New In Python 3.9 — Python 3.9.0 documentation](https://docs.python.org/3.9/whatsnew/3.9.html#math)  