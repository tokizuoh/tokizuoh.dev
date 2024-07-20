---
title: "時間計測するデコレータを作る"
date: 2020-10-25T22:02:43+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/python.png"
tags:
- python
---
  
デコレータ自作したことなかった。  
<!--more-->  
  
## 開発環境  
  
```bash
> python3 -V
Python 3.9.0
```
  
## デコレータ  
  
> A function returning another function, usually applied as a function transformation using the @wrapper syntax.  
> (from [Glossary — Python 3.9.0 documentation](https://docs.python.org/3/glossary.html))
  
## コード  
  
```python
def measure(func):
    import time  # ここに書けるんかいな
    def measure():
        start = time.time()
        func()
        elapsed_time = time.time() - start
        print("elapsed_time: {}".format(elapsed_time))
    return measure

@measure
def processing():
    for i in range(1000):
        for j in range(1000):
            pass

def main():
    processing()

main()

```
  
　
  
```bash
> python deco.py 
elapsed_time: 0.014774084091186523
```
  
## 参考
- [Glossary — Python 3.9.0 documentation](https://docs.python.org/3/glossary.html)