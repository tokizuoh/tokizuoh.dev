---
title: "UIStackViewの子要素のleadingを個別に付ける"
date: 2021-11-07T12:56:23+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
タイトル長い！  
  
<!--more-->  
  
## 開発環境  
  
```bash
Xcode 13.1
Build version 13A1030d
```
  
## こんなレイアウトを組む時
  
![](./1.png)
  
UIStackViewの中に青と黄のUIViewを配置して、黄の leading のみ個別で制約を付けたい時。
  
## こうする
  
![](./2.png)  
  
UIStackView の alignment を trailing にする。  
子要素の trailing の制約が定まることで、leading の制約が付けられるようになる。  
  