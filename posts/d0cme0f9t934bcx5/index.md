---
title: "Docker で Go の環境構築をする時のテンプレートを整備した"
date: 2021-08-07T22:06:06+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/docker_go.png"
tags:
- docker
- golang
---
  
備忘録。改善していきたい。  
  
<!--more-->  
  
## はじめに
Go の開発を行うときは Docker で環境を都度構築している。  
  
毎回自分の過去の GitHub リポジトリから手動でファイルをコピーしていたのが煩わしかったので、ある程度自動化した。  
  
## つくったもの  
  
```bash
> ls

> gosu
successfully copied 'go-docker-template' to the current directory!
Enjoy programming :)

> la -1a
.gitignore
Dockerfile
README.md
docker-compose.yml
```
  
コマンド一つで、自分が Go で何か開発する時の最低限必要なファイルをカレントディレクトリ配下にコピーするようにした。  
  
## 手順
  
1. [tokizuoh/go-docker-template](https://github.com/tokizuoh/go-docker-template) をローカルに git clone

2. 手順1で git clone したファイルをカレントディレクトリにコピーするシェルスクリプトを書く

```bash
#!bin/sh
fs[0]=docker-compose.yml
fs[1]=Dockerfile
fs[2]=README.md
fs[3]=.gitignore

for f in "${fs[@]}"
do
    cf=`printf "{手順1を clone した場所}}/%s" $f`
    cp $cf ./
done

echo "successfully copied 'go-docker-template' to the current directory!"
echo "Enjoy programming :)"
```

3. .zshrc で手順2のシェルスクリプトのエイリアスを設定
  
```bash
# go-docker-template
alias gosu="sh {手順2 のシェルスクリプト名}"
```
  
## おわりに
  
これでかなり開発開始時の手間が少なくなった。  
良い方法が見つかり次第、順次取り入れていく。  
  