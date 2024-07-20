---
title: "MVP (Passive View) を試す"
date: 2021-09-12T14:11:54+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
参考: [PEAKS（ピークス）｜iOSアプリ設計パターン入門](https://peaks.cc/books/iOS_architecture)  
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 12.3
Build version 12C33
```
  
## MVP (Model-View-Presenter)
  
- 画面の描画処理とプレゼンテーションロジックを分離するGUIアーキテクチャ
- 作業分担がしやすい: View, Presenter で各々でお互いを protocol として宣言し利用するため (I/Fが分かっていれば作業分担が可能)
- テスト容易性: 上記と同様で、protocol として I/F が分かっているのでテストが書きやすい
  
## Passive View
  
MVPの文脈で、データの同期方法が2パターン存在するが、今回は Passive View を使う。  
  
- フロー同期
- 処理の流れが追いやすい
- 更新するViewの数とその参照の数が比例するので、Viewの数が多くなるほど参照の管理が大変になる
  
## コード
  
[[#86] 自作の単語を送ったら同じ母音を持つ単語のリストが返るAPIをアプリから使う](https://github.com/tokizuoh/Pendula/pull/88)  
  
- [Model](https://github.com/tokizuoh/Pendula/pull/88/files#diff-19e9f8a9ca04454f7691c86dbc0a6765459c3be5383c62e1bb4db685efac84d3): API通信
- [View](https://github.com/tokizuoh/Pendula/pull/88/files#diff-ca862acad5c5e2d618c05656692b7b24e1efe41dbcc158270155fc5bc0ace924): 画面更新の通知を受け取り、更新する
- [Presenter](https://github.com/tokizuoh/Pendula/pull/88/files#diff-8c54dba6fc2596b1a96a70dd21e613f0fddfb9f476eaa04f9cebd1c3fa9f8c44): ModelとViewの橋渡し, Viewのためのデータ変形を担う
  
## 感想
  
- [ボタン押下時の処理](https://github.com/tokizuoh/Pendula/pull/88/files#diff-ca862acad5c5e2d618c05656692b7b24e1efe41dbcc158270155fc5bc0ace924R26)）は PresenterInput なる概念を導入して抽象化できそう
- 画面更新、ロジックなどの責務の分離が綺麗にできた
  
## 参考  
  
- [PEAKS（ピークス）｜iOSアプリ設計パターン入門](https://peaks.cc/books/iOS_architecture)  
  