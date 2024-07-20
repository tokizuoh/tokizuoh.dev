---
title: "現在のブランチ名に紐づくissueのURLをブラウザで開く"
date: 2021-10-10T13:46:28+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/command.png"
tags:
- command
---
  
ブラウザから辿るより楽。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> git --version
git version 2.30.1 (Apple Git-130)
```
  
## コード
  
```bash
#!/bin/zsh

# get "github.com/{user_name}/{repository_name}" of "https://github.com/{user_name}/{repository_name}/issue/{issue_number}"
repository_url=`git config --local --get remote.origin.url | sed -e 's/:/\//g' | sed -e 's/.git//g'`
base_url=`echo $repository_url | cut -d '@' -f 2`

# get "{issue_number}" of "https://github.com/{user_name}/{repository_name}/issue/{issue_number}"
issue_number=`git rev-parse --abbrev-ref HEAD | cut -d '#' -f 2`

# URLをブラウザで開く
open "https://${base_url}/issues/${issue_number}"
```
  
## git config --local --get remote.origin.url
  
```bash
> git config --local --get remote.origin.url
git@github.com:{user_name}/{repository_name}.git
```
  
issueのURLの基礎となる文字列を取得。  
  
## git rev-parse --abbrev-ref HEAD
  
```bash
> git rev-parse --abbrev-ref HEAD
impl/#96

> git branch | grep '*'
* impl/#96
```
  
現在のブランチ名を取得。  
  
## 参考  
  
- [Git - git-rev-parse Documentation](https://git-scm.com/docs/git-rev-parse)  
  