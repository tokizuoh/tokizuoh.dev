---
title: "言語処理100本ノック 第2章: Unixコマンド"
date: 2020-10-02T22:38:08+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/command.png"
tags:
- command
---
  
UNIXコマンドの練習。（おそらく本来の用途とは異なる）  
<!--more-->  
  
## リンク  
[第2章: UNIXコマンド - NLP100 2020](https://nlp100.github.io/ja/ch02.html)  
  
## 10. 行数のカウント  
  
```bash
> wc popular-names.txt | awk '{print $1}'
2780
```
  
## 11. タブをスペースに置換  
  
```bash
> expand -t 1 popular-names.txt        
Mary F 7065 1880
Anna F 2604 1880
Emma F 2003 1880
Elizabeth F 1939 1880
Minnie F 1746 1880
...
```
  
## 12. 1列目をcol1.txtに，2列目をcol2.txtに保存  
  
```bash
> cut -f 1 popular-names.txt > col1.txt  
```
  
　
  
#### 確認  
  
```bash
> cat col1.txt | head -n 5
Mary
Anna
Emma
Elizabeth
Minnie
```
  
col2.txtも同様に行う。  
  
## 13. col1.txtとcol2.txtをマージ  
  
```bash
paste col1.txt col2.txt > thirteen.txt
```
  
　
  
#### 確認  
  
```bash
> cat thirteen.txt | head -n 5
Mary	F
Anna	F
Emma	F
Elizabeth	F
Minnie	F
```
  
## 14. 先頭からN行を出力  
  
```bash
> read v && cat popular-names.txt | head -n $v
5
Mary	F	7065	1880
Anna	F	2604	1880
Emma	F	2003	1880
Elizabeth	F	1939	1880
Minnie	F	1746	1880
```
  
`read v | cat ...` だとreadが終わる前にcatが実行されてしまう。  
`read v; cat...` で同様の結果が得られる。  
  
## 15. 末尾のN行を出力
  
```bash
> read v && cat popular-names.txt | tail -n $v
5
Benjamin	M	13381	2018
Elijah	M	12886	2018
Lucas	M	12585	2018
Mason	M	12435	2018
Logan	M	12352	2018
```
  
## 16. ファイルをN分割する  
  
```bash
> read v && split -l $v popular-names.txt hoge-  
```
  
　
  
#### 確認  
  
```bash
> ls | grep hoge | xargs wc | awk '{print $1}'
100
100
100
100
100
100
100
...
80
2780
```
  
## 17. １列目の文字列の異なり  
  
```bash
> cut -f 1 popular-names.txt | sort | uniq | head      
Abigail
Aiden
Alexander
Alexis
Alice
Amanda
Amelia
Amy
Andrew
Angela
```
  
## 18. 各行を3コラム目の数値の降順にソート  
  
```bash
> sort -k 3 -n -r popular-names.txt | head -n 20
Linda	F	99689	1947
Linda	F	96211	1948
James	M	94757	1947
Michael	M	92704	1957
Robert	M	91640	1947
Linda	F	91016	1949
Michael	M	90656	1956
Michael	M	90517	1958
James	M	88584	1948
Michael	M	88528	1954
Michael	M	88327	1955
John	M	88319	1947
James	M	87436	1946
James	M	87261	1951
James	M	87063	1952
Michael	M	86917	1961
James	M	86857	1949
Robert	M	86604	1952
Robert	M	86351	1951
Robert	M	86298	1954
```
  
## 19. 各行の1コラム目の文字列の出現頻度を求め，出現頻度の高い順に並べる
  
```bash
> cut -f 1 popular-names.txt | awk '{count[$0]++}END{for(i in count) print count[i], i}' | sort -n -r
118 James
111 William
108 Robert
108 John
92 Mary
75 Charles
74 Michael
73 Elizabeth
70 Joseph
60 Margaret
58 Thomas
58 George
57 David
51 Richard
45 Helen
...
1 Lori
1 Laura
1 Kelly
1 Julie
1 Crystal
1 Carolyn
```
  
## 参考  
- [【 wc 】コマンド――テキストファイルの文字数や行数を数える：Linux基本コマンドTips（62） - ＠IT](https://www.atmarkit.co.jp/ait/articles/1611/07/news026.html)  
- [Linuxコマンドを連続して使うには - Qiita](https://qiita.com/egawa_kun/items/714394609eef6be8e0bf)  
- [【 uniq 】コマンド――重複している行を削除する：Linux基本コマンドTips（64） - ＠IT](https://www.atmarkit.co.jp/ait/articles/1611/14/news021.html)  
- [【 sort 】コマンド――テキストファイルを行単位で並べ替える：Linux基本コマンドTips（63） - ＠IT](https://www.atmarkit.co.jp/ait/articles/1611/09/news020.html)  
  