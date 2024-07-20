---
title: "Command Challenge (1-11)"
date: 2021-02-01T20:53:47+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/command.png"
tags:
- command
---
  
bash(というよりLinuxコマンド)強化週間。  
https://cmdchallenge.com/  
  
<!--more-->  
  
## 1  
  
> Your first challenge is to print "hello world" on the terminal in a single command.  
  
```bash
printf 'hello world' 
```
  
## 2
  
> Print the current working directory.  
  
```bash
pwd
```
  
## 3
  
> List names of all the files in the current directory, one file per line.
  
```bash
ls | grep '' 
```
  
lsを行った時点では1行につき1ファイル表示にならないが、grepに渡した後は1行につき1ファイル表示になる。  
  
## 4
  
> There is a file named access.log in the current directory. Print the contents.

```bash
cat access.log 
```
  
## 5
  
> Print the last 5 lines of "access.log".
  
```bash
tac access.log | head -n 5 | tac 
```
  
tac（catの逆）を2回使って戻している。  
  
## 6
  
> Create an empty file named take-the-command-challenge in the current working directory.
  
```bash
touch take-the-command-challenge 
```
  
## 7
  
> Create a directory named tmp/files in the current working directory
  
```bash
mkdir -p tmp/files
```
  
pはparents。  
  
## 8
  
> Copy the file named take-the-command-challenge to the directory tmp/files  
  
```bash
cp take-the-command-challenge tmp/files 
```
  
## 9
  
> Move the file named take-the-command-challenge to the directory tmp/files
  
```bash
mv take-the-command-challenge tmp/files 
```
  
## 10
  
> Create a symbolic link named take-the-command-challenge that points to the file tmp/files/take-the-command-challenge.
  
```bash
ln -s tmp/files/take-the-command-challenge take-the-command-challenge
```
  
## 11
  
> Delete all of the files in this challenge directory including all subdirectories and their contents.  
  
```bash
find . -delete  
```
  
たった一つのオプションをつけるだけでfindは恐ろしいコマンドになる。  
  
## 参考  
  
- [【 find 】コマンド（応用編その2）――検索したファイルを指定したフォーマットで表示する：Linux基本コマンドTips（33） - ＠IT](https://www.atmarkit.co.jp/ait/articles/1607/20/news024.html)  
  