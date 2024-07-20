---
title: "Linuxコマンドで最後にアクセスしたファイル名を取得する"
date: 2021-02-23T19:08:23+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/command.png"
tags:
- command
---
  
<!--more-->  
  
## コマンド  
  
```bash
> ls -tlu | head -n 2 | tail -n 1 | awk '{print $9}'
```
  
## 解説  
  
```bash
# 下記のファイルに対し、最後にアクセスしたファイル名を取得する
> ls
fuga.txt hoge.txt piyo.txt

# ls
# -t: 日時でソート
# -l: 詳細表示
# -u: lと併用したとき、最終アクセス日を表示する
> ls -tlu
total 0
-rw-r--r--  1 tokizo  staff  0  2 23 19:12 piyo.txt
-rw-r--r--  1 tokizo  staff  0  2 23 19:11 fuga.txt
-rw-r--r--  1 tokizo  staff  0  2 23 19:10 hoge.txt

# head
# -n {num}: 先頭{num}行を表示
> ls -tlu | head -n 2  
total 0
-rw-r--r--  1 tokizo  staff  0  2 23 19:12 piyo.txt

# tail
# -n {num}: 末尾{num}行を表示
> ls -tlu | head -n 2 | tail -n 1  
-rw-r--r--  1 tokizo  staff  0  2 23 19:12 piyo.txt

# awk
> ls -tlu | head -n 2 | tail -n 1 | awk '{print $9}'
piyo.txt

# awk 'print $0'
> ls -tlu | head -n 2 | tail -n 1 | awk '{print $0}'
-rw-r--r--  1 tokizo  staff  0  2 23 19:12 piyo.txt
 
```
  
