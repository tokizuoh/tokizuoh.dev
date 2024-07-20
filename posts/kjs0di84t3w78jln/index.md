---
title: "最新commitのハッシュだけを標準出力させる"
date: 2020-12-09T23:15:07+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/git.png"
tags:
- git
---
  
`git log -1` すれば済む話だが、`less` みたいに画面（領域）跨ぐのが煩わしかった。  
<!--more-->  
  
## 開発環境  
  
```bash
> git --version
git version 2.24.3 (Apple Git-128)
```
  
## コード (bash)
  
```bash
#!/bin/sh
str=(`git log -1`)  # ()で空白区切り文字列を配列に
echo ${str[1]}
```
  
