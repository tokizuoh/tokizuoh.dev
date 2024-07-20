---
title: "commitしてない差分がある状態ではgit stash applyできない"
date: 2020-11-18T21:17:49+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/git.png"
tags:
- git
---
  
エラー文は最後まで読もう。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> git --version
git version 2.24.3 (Apple Git-128)
```
  
## git stash apply
  
```bash
> git stash apply
error: Your local changes to the following files would be overwritten by merge:
	text.txt
Please commit your changes or stash them before you merge.
Aborting
```
stashした作業をapplyで戻す時、commitしてない差分があると上記のエラーが出る。commitしてからapplyしよう。  
  
~~エラーに出くわした時はエラー文の一行目しか読まず、「コンフリクト対処ファイルはapplyできないんだな」と早とちりしてしまった。~~  
  