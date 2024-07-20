---
title: "git-update-index を試す"
date: 2021-08-21T14:25:39+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/git.png"
tags:
- git
---
  
そもそも git の文脈での index が分からなかったので調べた。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> git --version
git version 2.24.3 (Apple Git-128)
```
  
## git-update-index とは
  
> git-update-index - Register file contents in the working tree to the index  
> [Git - git-update-index Documentation](https://git-scm.com/docs/git-update-index)  
  
git-update-index は working treeの内容を index に登録する。  
  
... working tree, index って何だ？  
  
## working tree とは
  
[git-worktree](https://git-scm.com/docs/git-worktree) を使ったことがある。  
これは作業ディレクトリをブランチごとに扱うコマンド。  
  
> git-worktree - Manage multiple working trees  
> [Git - git-worktree Documentation](https://git-scm.com/docs/git-worktree)  
  
git-worktree は複数の working tree を管理する。  
  
上記から、working tree == 作業ディレクトリ という理解で進める。  
  
## index とは
  
[git-add](https://git-scm.com/docs/git-add) の説明に index が登場する。  
  
> git-add - Add file contents to the index  
> [Git - git-add Documentation](https://git-scm.com/docs/git-add)  
  
ファイルの内容を index に登録する。  
git-add を行ったファイルは index に登録される。  
  
上記から、index == git-add したファイルが登録される場所 という理解で進める。  
  
## git-update-index とは
  
本題に戻る。  
git-add と git-update-index は index に何かを登録するという観点で共通している。  
  
- git-update-index: working tree の内容
- git-add: ファイルの内容
  
git-update-index と git-add は何が違うのか？  
  
## git-update-index と git-add の違い
  
[What is the difference between "git add" and "git update-index" - Stack Overflow](https://stackoverflow.com/questions/38585812/what-is-the-difference-between-git-add-and-git-update-index)  
  
他にも違いが気になった人がいたみたい。以下のことが書かれていた。  

- git-add は内部的に git-update-index を使用しているかもしれない  
- コミットしたくないファイルの変更がある場合に、以下のコマンドを使う時がある  
  
```bash
git update-index --assume-unchanged <file>
```
  
　
  
#### git-add は内部的に git-update-index を使用しているかもしれない
  
git-update-index のドキュメントに以下の記述があった。  
  
> See also git-add[1] for a more user-friendly way to do some of the most common operations on the index.  
> [Git - git-update-index Documentation](https://git-scm.com/docs/git-update-index)  
  
index で最も一般的な操作を実行するためのよりユーザーフレンドリーな方法については git-add を参照。  
index にファイルを登録する手段として、 git-add と git-update-index があり、ユーザーが使いやすいのは git-add という理解で進める。  
  
#### コミットしたくないファイルの変更がある場合に、以下のコマンドを使う時がある
  
```
git update-index --assume-unchanged <file>
```
  
> In order to set "assume unchanged" bit, use --assume-unchanged option. To unset, use --no-assume-unchanged.  
> [Git - git-update-index Documentation](https://git-scm.com/docs/git-update-index)  
  
"変更されていないと仮定する" ビットを設定するためのオプションが --assume-unchanged 。  
解除するには --no-assume-unchanged を使う。  
  
使ってみる。  
  
1. ファイルに変更を加える
2. git update-index --assume-unchanged {file}
3. git ls-files -v （`h {file}` が表示）
4. git update-index --no-assume-unchanged {file}
5. git ls-files -v （`H {file}` が表示）
  
3, 5で h/H が出力された。これはキャッシュ状態を表す。小文字が assume unchanged 状態を表す。  
また、1の後に `git add {file}` を行った後に `git ls-files -v` を行うと H が出力されるので、以下の操作は index に登録する処理としては同じ動きをする。  
  
- git add {file}
- git update-index {file}
  
## まとめ
  
- git-update-index は git-add と基本的に同じ処理が可能
- コミットしたくないが、ファイルの変更を index に登録したいときは `git update-index --assume-unchanged {file}` が有効
  
## 参考  
  
- [Git - git-update-index Documentation](https://git-scm.com/docs/git-update-index)  
- [Git - git-worktree Documentation](https://git-scm.com/docs/git-worktree)  
- [Git - git-add Documentation](https://git-scm.com/docs/git-add)  
- [Git - git-ls-files Documentation](https://git-scm.com/docs/git-ls-files)  
- [What is the difference between "git add" and "git update-index" - Stack Overflow](https://stackoverflow.com/questions/38585812/what-is-the-difference-between-git-add-and-git-update-index)  
  