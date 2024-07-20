---
title: "(保留) concurrent を使って並列処理を試す"
date: 2021-12-25T12:01:38+09:00
draft: true
thumbnail: "https://tokizuoh.dev/images/thumbnail/python.png"
tags:
- python
---
  
[1日1問、半年以内に習得 シェル・ワンライナー160本ノック](https://gihyo.jp/book/2021/978-4-297-12267-6) を Python で解いていて、処理に悩んだので記事として残す  
  
<!--more-->  
  
## 開発環境  
  
```bash
> docker-compose exec app python -V
Python 3.10.1
```
  
Dockefileなど > [tokizuoh/geminos](https://github.com/tokizuoh/geminos)  
  
## お題
  
下記処理の効率化を考える
  
- tmp ディレクトリ配下に、1 から 10000 までの数字を名前とするファイル名を生成し、それらのファイル名の頭に 0 を付けて、ファイル名を 5 桁に揃える ([書籍](https://gihyo.jp/book/2021/978-4-297-12267-6)の問題3 改) 
  
## 最終形  
  
```python
# TODO: ここに最終形を乗せる
```
  
## コード例1: 愚直
  
```python
import os
import time

TARGET_PATH = "./tmp/"
LIMIT = 10000

def processing():
    # generate
    for i in range(LIMIT):
        path = TARGET_PATH + str(i + 1)
        f = open(path, mode="w")

    # rename file
    for i in range(LIMIT):
        file_name = str(i + 1).zfill(5)
        before_path = TARGET_PATH + str(i + 1)
        after_path = TARGET_PATH + file_name
        os.rename(before_path, after_path)

def main():
    start = time.time()

    processing()

    elapsed_time = time.time() - start
    print("elapsed_time: {} [sec]".format(elapsed_time))  # elapsed_time: 49.1166455745697 [sec]

if __name__ == "__main__":
    main()
```
  
49秒。  
  
## コード例2: concurrentを導入
  
```python
import os
import time
from concurrent.futures import ThreadPoolExecutor

TARGET_PATH = "./tmp/"
LIMIT = 10000

# generate
def processing1(i):
    path = TARGET_PATH + str(i + 1)
    f = open(path, mode="w")

# rename file
def processing2(i):
    file_name = str(i + 1).zfill(5)
    before_path = TARGET_PATH + str(i + 1)
    after_path = TARGET_PATH + file_name
    os.rename(before_path, after_path)

def main():
    start = time.time()
    
    with ThreadPoolExecutor(2, thread_name_prefix="tmp") as executer:
        for i in range(LIMIT):
            executer.submit(processing1, i)
        for i in range(LIMIT):
            executer.submit(processing2, i)

    elapsed_time = time.time() - start
    print("elapsed_time: {} [sec]".format(elapsed_time))  # elapsed_time: 46.10425353050232 [sec]

if __name__ == "__main__":
    main()
```
  
46秒。手探りで書いてみたが並列の恩恵が受けられていなさそう。見直す。  
  
## コード例3: concurrentを導入②
  
```python
import os
import time
from concurrent.futures import ProcessPoolExecutor

TARGET_PATH = "./tmp/"
LIMIT = 10000

# generate
def processing1(i):
    path = TARGET_PATH + str(i + 1)
    f = open(path, mode="w")

# rename file
def processing2(i):
    file_name = str(i + 1).zfill(5)
    before_path = TARGET_PATH + str(i + 1)
    after_path = TARGET_PATH + file_name
    os.rename(before_path, after_path)

def main():
    start = time.time()
    with ProcessPoolExecutor(os.cpu_count()) as executer:
        for i in range(LIMIT):
            executer.submit(processing1, i)

        for i in range(LIMIT):
            executer.submit(processing2, i)

    elapsed_time = time.time() - start
    print("elapsed_time: {} [sec]".format(elapsed_time))  # elapsed_time: 48.422327518463135 [sec]


if __name__ == "__main__":
    main()
```
  
48秒。 改善が見られない。  
  
- ProcessPoolExecutor を利用
- workerの数をosのcpu数に変更
  
処理自体に問題がありそうなのでシンプルな例で並列処理をさせてみる。  
  
## 脱線1
  
sleep の速度を愚直と並列実行で比較する。
  
#### 愚直
  
```python
import os
import time

LIMIT = 10000

def processing():
    for i in range(LIMIT):
        time.sleep(0.001)

    for i in range(LIMIT):
        time.sleep(0.001)

def main():
    start = time.time()

    processing()

    elapsed_time = time.time() - start
    print("elapsed_time: {} [sec]".format(elapsed_time))  # elapsed_time: 31.47097635269165 [sec]

if __name__ == "__main__":
    main()

```
  
31秒。  
  
#### 並列（プロセス）
  
```python
import os
import time
from concurrent.futures import ProcessPoolExecutor

LIMIT = 10000

def processing1(i):
    time.sleep(0.001)

def processing2(i):
    time.sleep(0.001)

def main():
    start = time.time()
    with ProcessPoolExecutor(os.cpu_count()) as executer:
        for i in range(LIMIT):
            executer.submit(processing1, i)

        for i in range(LIMIT):
            executer.submit(processing2, i)

    elapsed_time = time.time() - start
    print("elapsed_time: {} [sec]".format(elapsed_time))  # elapsed_time: 7.871685028076172 [sec]


if __name__ == "__main__":
    main()
```
  
7秒。
  
#### 並列 (スレッド)
  
```python
import os
import time
from concurrent.futures import ThreadPoolExecutor

LIMIT = 10000

def processing1(i):
    time.sleep(0.001)

def processing2(i):
    time.sleep(0.001)

def main():
    start = time.time()
    with ThreadPoolExecutor(10) as executer:
        for i in range(LIMIT):
            executer.submit(processing1, i)

        for i in range(LIMIT):
            executer.submit(processing2, i)

    elapsed_time = time.time() - start
    print("elapsed_time: {} [sec]".format(elapsed_time))  # elapsed_time: 2.8570642471313477 [sec]

if __name__ == "__main__":
    main()

```
  
2秒。  
  
## 参考  
  
- [1日1問、半年以内に習得 シェル・ワンライナー160本ノック：書籍案内｜技術評論社](https://gihyo.jp/book/2021/978-4-297-12267-6)  
  