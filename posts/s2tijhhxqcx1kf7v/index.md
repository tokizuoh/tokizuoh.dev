---
title: "forbiddenfruitを使って組み込み型を拡張する"
date: 2021-01-24T21:37:13+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/python.png"
tags:
- python
---
  
小ネタ。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> python3 -V
Python 3.9.0
```
  
## forbiddenfruit  
  
> This project allows Python code to extend built-in types.  
> [forbiddenfruit · PyPI](https://pypi.org/project/forbiddenfruit/)  
  
forbiddenfruitは組み込み型の拡張を可能とするライブラリ。  
  
　
  
```python
>>> numbers = ["1", "2", "3"]
>>> "-".join(numbers)
'1-2-3'
```
  
この処理がモヤるので、解決策としてlistにjoinを拡張してみた。（リストを結合するのに文字列始まりなのがモヤる）  
  
## コード  
  
```python
from forbiddenfruit import curse

def join(self, separator=""):
    return separator.join(self)

curse(list, "join", join)

numbers = ["1", "2", "3"]

a = numbers.join("-")  # スッキリ！
b = "-".join(numbers)
assert a == b
```
  
結局内部で既存の実装を使っているが、長年のモヤモヤが晴れたので良し。  
  
## 参考  
  
- [forbiddenfruit · PyPI](https://pypi.org/project/forbiddenfruit/)  
  