---
title: "Linuxコマンド練習問題(対CSV風ファイル) 3問"
date: 2021-02-06T20:13:02+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/command.png"
tags:
- command
---
  
bash強化週間。  
  
<!--more-->  
  
## 問題  
*sample.txt*  
  
```bash
"name","age","blood type"
"Samurai","52","B"
"Kinako-mochi","10","AB"
"Warabi-mochi","35","O"
"Ishikawa","28","A"
```
  
1. name列を抽出し、ヘッダーを除いて出力してください。  
2. 各行のage列とblood type列を"-"で結合し、ヘッダーを除いて出力してください。  
3. age列の各値を数値に直した際の合計を出力してください。  
  
## 1. name列を抽出し、ヘッダーを除いて出力してください。  
  
```bash
> cut -d',' -f1 sample.txt | tail -n +2 
"Samurai"
"Kinako-mochi"
"Warabi-mochi"
"Ishikawa"
```
  
## 2. 各行のage列とblood type列を"-"で結合し、ヘッダーを除いて出力してください。
  
```bash
> cut -d',' -f1,2 sample.txt | awk -F "," '{printf "%s-%s\n",$1, $2}' | tail -n +2
"Samurai"-"52"
"Kinako-mochi"-"10"
"Warabi-mochi"-"35"
"Ishikawa"-"28"
```
  
## 3. age列の各値を数値に直した際の合計を出力してください。  
  
```bash
> cut -d',' -f2 sample.txt | tr -d '"' | tail -n +2 | awk '{t+=$0} END{print t;}'
125
```
  
## 参考  
- [実践 bashによるサイバーセキュリティ対策――セキュリティ技術者のためのシェルスクリプト活用術](https://www.oreilly.co.jp/books/9784873119052/)  
  