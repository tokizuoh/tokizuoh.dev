---
title: "imos法でセルの管理をする"
date: 2021-04-03T12:23:43+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
- algorithms
---
  
唐突な競プロ要素。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 12.3
Build version 12C33
```
  
## imos法  
  
> いもす法とは，累積和のアルゴリズムを多次元，多次数に拡張したものです．  
> [いもす法 - いもす研 (imos laboratory)](https://imoz.jp/algorithms/imos_method.html#:~:text=%E3%81%84%E3%82%82%E3%81%99%E6%B3%95%E3%81%A8%E3%81%AF,%E3%81%93%E3%81%A8%E3%81%8C%E3%82%8F%E3%81%8B%E3%81%A3%E3%81%A6%E3%81%84%E3%81%BE%E3%81%99%EF%BC%8E)  
  
具体例を追って imos法 を理解していく。  
  
---
  
```
あなたは旅館を経営しています。
  
旅館では[ルール]の内容に従って、一週間の各曜日の客の総数を管理しています。  
どうすれば客の管理を効率化できますか？  
[客の入退場予定]を使って、効率化の例を示してください。  
  
[ルール]
・ 1×7 の表を使用（1つのセルが各曜日に対応）
  
[客の入退場予定]
1. 入場: 0曜日 | 退場: 2曜日
2. 入場: 3曜日 | 退場: 6曜日
3. 入場: 1曜日 | 退場: 4曜日
```
  
#### 愚直な解法  
  
- 方針：「入場する曜日」 から 「退場する前日の曜日」までの間のすべてのセルの値を +1  
- 計算量：Nを曜日数, Mを客の入退場数とした場合 **O(NM)**  
  
　
  
*初期状態*
```
| 0 | 1 | 2 | 3 | 4 | 5 | 6 |  # 曜日 
-----------------------------
| 0 | 0 | 0 | 0 | 0 | 0 | 0 |  # 各曜日の客の総数 
```
  
　
  
*1. 入場: 0曜日 | 退場: 2曜日*
```
| 0 | 1 | 2 | 3 | 4 | 5 | 6 |  
-----------------------------
| 1 | 1 | 0 | 0 | 0 | 0 | 0 |  
```
  
　
  
*2. 入場: 3曜日 | 退場: 6曜日*
```
| 0 | 1 | 2 | 3 | 4 | 5 | 6 |  
-----------------------------
| 1 | 1 | 0 | 1 | 1 | 1 | 0 |  
```
  
　
  
*3. 入場: 1曜日 | 退場: 4曜日*
```
| 0 | 1 | 2 | 3 | 4 | 5 | 6 |  
-----------------------------
| 1 | 2 | 1 | 2 | 1 | 1 | 0 |  
```
  
　
  
効率が悪い。  
7日泊まる客が10000人いる場合、 7 * 10000 回 セルを更新する必要がある。  
  
#### imos法を使った解法  
  
- 方針：「入場する曜日」 のセルの値を +1、「退場する曜日」のセルの値を -1 して、最後に累積和を取る  
- 計算量：Nを曜日数, Mを客の入退場数とした場合 **O(N+M)**  
  
　
  
*初期状態*
```
| 0 | 1 | 2 | 3 | 4 | 5 | 6 |  # 曜日 
-----------------------------
| 0 | 0 | 0 | 0 | 0 | 0 | 0 |  # 各曜日の客の総数 
```
  
　
  
*1. 入場: 0曜日 | 退場: 2曜日*
```
| 0 | 1 | 2 | 3 | 4 | 5 | 6 |  
-----------------------------
| 1 | 0 | -1| 0 | 0 | 0 | 0 |  
```
  
　
  
*2. 入場: 3曜日 | 退場: 6曜日*
```
| 0 | 1 | 2 | 3 | 4 | 5 | 6 |  
-----------------------------
| 1 | 0 | -1| 1 | 0 | 0 | -1|  
```
  
　
  
*3. 入場: 1曜日 | 退場: 4曜日*
```
| 0 | 1 | 2 | 3 | 4 | 5 | 6 |  
-----------------------------
| 1 | 1 | -1| 1 | -1| 0 | -1|  
```
  
　
  
最後に前から後ろに足していく（累積和）  
  
```
| 0 | 1 | 2 | 3 | 4 | 5 | 6 |  # 曜日 
-----------------------------
| 1 | 1 | -1| 1 | -1| 0 | -1|  # 各曜日の客の総数 
-----------------------------
| 1 | 2 | 1 | 2 | 1 | 1 | 0 |  # 累積和
```
  
　
  
愚直な解法の **O(NM)** と比較して、 **O(N+M)** に収まり効率化された。  
  
## imos法（一次元）の練習問題  
  
練習問題を解くと理解が深まるのでおすすめ。  
  
- [AtCoder Beginner Contest 035 C - オセロ](https://atcoder.jp/contests/abc035/tasks/abc035_c)  
- [AtCoder Beginner Contest 127 C - Prison](https://atcoder.jp/contests/abc127/tasks/abc127_c)  
- [CPSCO2019 Session3 C - Camp Reception](https://atcoder.jp/contests/cpsco2019-s3/tasks/cpsco2019_s3_c)  
  
## imos法でセルの管理をする
  
本題。  
iOSアプリ内でcollectionViewの状態管理を imos法 で行った。  
  
[コード](https://github.com/tokizuoh/Pendula/pull/36)  
  
## gif
  
![](./1.gif)  
  
## 参考  
  
- [いもす法 - いもす研 (imos laboratory)](https://imoz.jp/algorithms/imos_method.html#:~:text=%E3%81%84%E3%82%82%E3%81%99%E6%B3%95%E3%81%A8%E3%81%AF,%E3%81%93%E3%81%A8%E3%81%8C%E3%82%8F%E3%81%8B%E3%81%A3%E3%81%A6%E3%81%84%E3%81%BE%E3%81%99%EF%BC%8E)  
- [AtCoder Beginner Contest 035 C - オセロ](https://atcoder.jp/contests/abc035/tasks/abc035_c)  
- [AtCoder Beginner Contest 127 C - Prison](https://atcoder.jp/contests/abc127/tasks/abc127_c)  
- [CPSCO2019 Session3 C - Camp Reception](https://atcoder.jp/contests/cpsco2019-s3/tasks/cpsco2019_s3_c)  