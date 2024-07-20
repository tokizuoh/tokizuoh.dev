---
title: "git merge --squash を試す"
date: 2021-10-17T14:11:22+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/git.png"
tags:
- git
---
  
<!--more-->  
  
## 開発環境  
  
```bash
> git --version
git version 2.33.1
```
  
## git merge --squash　{BRANCH_NAME}
  
現在のブランチと指定したブランチとの差分である複数 commit を1つのコミットにまとめる。  
  
## 前提
  
```bash
> git log --pretty=oneline
{COMMIT_HASH_E} (HEAD -> feature/#1) e
{COMMIT_HASH_D} d
{COMMIT_HASH_C} c
{COMMIT_HASH_B} (develop) b
{COMMIT_HASH_A} a
```
  
## やること
  
`feature/#1` ブランチのコミット（c, d, e）を一つのコミットにまとめて、`develop` にマージ
  
## やってみる
  
```bash
> git branch --contains
* develop

> git merge --squash feature/#1
Updating {COMMIT_HASH_B}..{COMMIT_HASH_E}
Fast-forward
Squash commit -- not updating HEAD
 text.txt | 4 ++++
 1 file changed, 4 insertions(+)
```
  
## 確認
  
```bash
> git log --pretty=oneline
{COMMIT_HASH__NEW} (HEAD -> develop, origin/develop) Squashed commit of the following:
{COMMIT_HASH_B} b
{COMMIT_HASH_A} a
```
  
無事まとまったコミットがマージされたことが分かった。  
コミットが綺麗になって良い。  
  
## 参考  
  
- [Git - git-merge Documentation](https://git-scm.com/docs/git-merge)  
  