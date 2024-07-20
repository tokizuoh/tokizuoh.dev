---
title: "checkout時の新規ブランチ名にprefixをつける"
date: 2020-10-22T23:02:33+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/git.png"
tags:
- git
---
  
Git Hooksを試してみた。  
<!--more-->  
  
## 開発環境  
  
```bash
> git --version
git version 2.24.3 (Apple Git-128)
```
  
## Git Hooks  
  
> Git has a way to fire off custom scripts when certain important actions occur.  
> (from [Git - Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks))  
  
git上の特定のアクションが発生した時に自作スクリプトを走らせることが出来る。  
　
## ブランチモデル  
  
GitFlowやGitHubFlowなどのブランチモデルを取り入れた開発環境下でのブランチ名にはprefixをつけることが多い。  
  
毎回prefixを入力するのは面倒なので、Git Hooksを使って自動でprefixをいれてみる。  
  
今回は試しにcheckoutしたブランチ名のprefixに `feature/` を付与する。  
  
## コード  
  
#### .git/hooks/post-checkout
  
```sh
#!/bin/sh
branch_name=`git symbolic-ref --short HEAD`
branch_name="feature/"${branch_name}
git branch -m ${branch_name}
```
  
　
  
#### Git Hooksのignoredをfalseに  
```bash
> git config advice.ignoredHook false  
```
  
　
  
#### シェルスクリプトの権限変更  
  
```bash
> chmod a+x ./post-checkout 
```
  
　
  
#### いざcheckout
  
```bash
> git checkout -b hoge0006

> git symbolic-ref --short HEAD
feature/hoge0006
```
  
## 参考
- [Git - Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)  
  