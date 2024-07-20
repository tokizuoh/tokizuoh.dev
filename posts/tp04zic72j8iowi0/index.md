---
title: "lsで一行ずつ表示する"
date: 2020-09-26T10:54:57+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/command.png"
tags:
- command
---
  
コマンド練習。  
<!--more-->  
  
## 概要  
`ls` で一行ずつファイル名を表示したい。  
複数の手法で試してみた。  
  
　
  
とりあえず `ls` したものを載せておく。  
  
```bash
> ls
fuga.txt   piyo.txt   test02.txt test04.txt test06.txt
hoge.txt   test01.txt test03.txt test05.txt test07.txt
```
  
## -1オプション  
  
```bash
> ls -1
fuga.txt
hoge.txt
piyo.txt
test01.txt
test02.txt
test03.txt
test04.txt
test05.txt
test06.txt
test07.txt
```
  
l（英字）ではなく、1(数字)。  
  
## awkを併用  
  
```bash
> ls -l | awk 'NR>1 {print $9}' 
fuga.txt
hoge.txt
piyo.txt
test01.txt
test02.txt
test03.txt
test04.txt
test05.txt
test06.txt
test07.txt
```
  
`ls -l | awk '{print $9}'` の実行結果の一行目は、`$9` に該当する表示項目が無い。  
そのため、改行のみとなり不要なので `NR>1` を加えた。  
NRは現在処理しているファイルの行番号を指す。  
`NR>1`とすることで、先頭行を非表示にしている。  
  
## sedを併用  
  
```bash
> ls | sed 's/ /\n/g'        
fuga.txt
hoge.txt
piyo.txt
test01.txt
test02.txt
test03.txt
test04.txt
test05.txt
test06.txt
test07.txt
```
  
空白を改行コードに置換。  
  
## xargsを併用  
  
```bash
> ls | xargs -I{}
fuga.txt
hoge.txt
piyo.txt
test01.txt
test02.txt
test03.txt
test04.txt
test05.txt
test06.txt
test07.txt
```
  
> Replace occurrences of replace-str in the initial arguments with names read from standard input. (from [xargs options (Finding Files)](https://www.gnu.org/software/findutils/manual/html_node/find_html/xargs-options.html))
  
`xargs -I {変数}` で標準入力から読み取った名前に置き換える。  
  
`ls | xargs -L 1` でも同じ結果が得られる。  
  
## 参考  
- [【 ls 】コマンド――ファイルを一覧表示する：Linux基本コマンドTips（26） - ＠IT](https://www.atmarkit.co.jp/ait/articles/1606/27/news018.html)  
- [【 awk 】コマンド（基本編）――テキストの加工とパターン処理を行う：Linux基本コマンドTips（115） - ＠IT](https://www.atmarkit.co.jp/ait/articles/1706/02/news017.html)  
- [【 sed 】 文字列の置換，行の削除を行う | 日経クロステック（xTECH）](https://xtech.nikkei.com/it/article/COLUMN/20060227/230879/)  
- [【 xargs 】コマンド――コマンドラインを作成して実行する：Linux基本コマンドTips（176） - ＠IT](https://www.atmarkit.co.jp/ait/articles/1801/19/news014.html)  
- [xargs options (Finding Files)](https://www.gnu.org/software/findutils/manual/html_node/find_html/xargs-options.html)  