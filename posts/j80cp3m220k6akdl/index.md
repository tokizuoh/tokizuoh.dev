---
title: "Python3.10の新機能を試す (PEP604, 613, 618)"
date: 2021-02-19T23:50:48+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/python.png"
tags:
- python
---
  
PEPはPython Enhancement Proposalの略。  
<!--more-->  
  
## 開発環境  
  
```bash
# Docker
> python -V
Python 3.10.0a5

# ローカル
> python -V
Python 3.8.5

> mypy --version
mypy 0.800
```
## PEP 604: New Type Union Operator
  
Unionを使う際の型アノテーションにはUnionが使用されていたが、`|` のみも許容するようになった。  
ここで、演算子のUnionと型のUnionが混在するので整理。  
  
#### 演算子のUnion  
  
Unionとはdictクラスのマージ演算のことを指す。（[PEP 584](https://www.python.org/dev/peps/pep-0584/)）
  
```python
d1 = {"hoge": 1}
d2 = {"fuga": 2, "hoge": 3}

d3 = d1 | d2  # {'hoge': 3, 'fuga': 2}
```
  
#### 型のUnion  
  
`Union[X, Y]` は X または Y を表す。（どちらかの型であればOK）  
  
```python
from typing import Union

U = Union[int, float]

def print_numeric(num: U):
    print(num)

# error: Argument 1 to "print_numeric" has incompatible type "str"; expected "Union[int, float]"
print_numeric("a")

# ok!
print_numeric(1)
```
  
#### PEP 604: New Type Union Operator  
本題。
  
```python
from typing import Union

# 今まで
# def print_numeric(num: Union[int, float]):
    # print(num)

# PEP 604
def print_numeric(num: int | float):
    print(num)

print_numeric(1)
```
  
## PEP 613: TypeAlias Annotation  
  
タイプエイリアスと普通の代入の区別がつきにくいので、タイプエイリアス用のアノテーションが追加された。  
  
```python
# 今まで
HogeInt = int

# PEP 613
HogeInt: TypeAlias = int
```
  
## PEP 618: Add Optional Length-Checking To zip
  
zip関数に `strict` というオプションが追加。  
このオプションがTrueのとき、zipに渡すイテラブルの長さが異なると例外を出す。  
  
```python
A = [1, 2, 3]
B = [7, 6, 5, 11]

# 今まで: エラーにならず、3回for文が実行される
for a, b in zip(A, B):
    print(a - b)

# PRP618: 4回目のfor文実行時に例外が出される。（3回までは通常通り実行される）
for a, b in zip(A, B, strict=True):
    print(a - b)
```
  
## Better error messages in the parser
  
構文解析時のエラーメッセージが改善された。  
  
```python
hoge = [1, 2, 3

# python3.8.5
# SyntaxError: unexpected EOF while parsing

# Python 3.10.0a5
# SyntaxError: '[' was never closed
```
  
## dockerコマンドメモ
忘れるのでメモ。  
  
```bash
# コンテナを起動しっぱなしにする
> docker run -itd {IMAGE_ID}

# コンテナに潜る
> docker exec -it {CONTAINER_ID} sh  
```

  
## 参考  
  
- [What's New In Python 3.10 — Python 3.10.0a5 ドキュメント](https://docs.python.org/ja/3.10/whatsnew/3.10.html#what-s-new-in-python-3-10)  
- [typing --- 型ヒントのサポート — Python 3.10.0a5 ドキュメント](https://docs.python.org/ja/3.10/library/typing.html#typing.Union)  
- [mypyで静的型チェックを導入する - け日記](https://ohke.hateblo.jp/entry/2020/10/03/230000)  
