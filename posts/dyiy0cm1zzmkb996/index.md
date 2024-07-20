---
title: "Hugoの記事タイトルを無心で決める"
date: 2020-08-23T14:12:26+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/hugo.png"
tags:
- hugo
---
  
CLIでランダム文字列生成→記事のmdファイル生成をシェルスクリプトにまとめた。  
<!--more-->  
  
## 動機  
  
> ブログのタイトルID（~/posts/ 以降の文字列）を決めるのが面倒。  
> 何でもいいので楽に決めたい。  
> ランダムな文字列を手元で生成するCLIあったら便利だな。  
>   
> (from [Swiftでランダム文字列を生成するCLIを作った](https://tokizuoh.dev/posts/jo4d9a0mm7hs975d/))  
  
今までは下記の手順でHugoの新規記事生成をしていた。  
  
```
1. 上記の記事で作成したCLIを実行  
2. 出力のランダム文字列を手元でコピー  
3. `hugo new posts/{ランダム文字列}/index.md`
```
  
　
  
上記の流れを1つのコマンドで行いたく、シェルスクリプトを書いた。  
  
## 下準備  
  
[![tokizuoh/randomizer - GitHub](https://gh-card.dev/repos/tokizuoh/randomizer.svg)](https://github.com/tokizuoh/randomizer)
  
cloneしてREADMEの通りに実行。  
  
## シェルスクリプト  
  
### newrand.sh
```
#!/bin/bash
shopt -s expand_aliases
alias randomizer='{hogefugapiyo}/randomizer/.build/debug/randomizer'
hoge=`randomizer`
`hugo new posts/$hoge/index.md`
```
  
初めてのシェルスクリプトだったので寄り道してみた。  
解説↓
  
　
  
```
shopt -s expand_aliases
alias randomizer='{hogefugapiyo}/randomizer/.build/debug/randomizer'
```
シェルスクリプト内で既存のエイリアスは実行できない。  
expand_aliasesを有効にした後にエイリアスを追加。  
  
　
  
```
hoge=`randomizer`
```
変数hogeに `randomizer` コマンドの標準出力を格納。  
  
　
  
```
`hugo new posts/$hoge/index.md`
```
  
コマンド内で変数を使うには `$` が必要。  
  
　
  
あとは実行するのみ。
  
```
> ./newrand.sh    
```
  
## 参考  
- [シェルスクリプト内でaliasを設定する - Qiita](https://qiita.com/narumi_/items/77002a12d62585da1fbe)  
- [【 shopt 】コマンド（応用編その1）――コマンドライン履歴の扱い方を変更する：Linux基本コマンドTips（362） - ＠IT](https://www.atmarkit.co.jp/ait/articles/1912/12/news034.html)  
  