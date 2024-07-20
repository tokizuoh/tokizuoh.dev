---
title: "--theirs と --ours"
date: 2020-07-20T23:24:52+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/git.png"
tags:
- Git
---
コンフリクト起こると焦る。  
<!--more-->  
  
## 結論  
マージ時にコンフリクトが起きた際の解決方法。  
  
master ブランチに対して、 secondary ブランチをマージする場合を例にする。  
  
## git checkout --own .  
コンフリクト箇所を master ブランチ（被マージブランチ）で解消。  
  
## git checkout --their .  
コンフリクト箇所を secondary ブランチ（マージ元ブランチ）で解消。  
  
## 検証  
  
1. master ブランチで `main.swift` (なんでも良い) を作成しcommit。  
2. master ブランチから secondary ブランチを切り出す。  
3. master ブランチで `main.swift` に変更を加えてcommit。  
4. secondary ブランチで `main.swift` に変更を加えてcommit。  
5. master ブランチに対して secondary ブランチをマージさせると、下記のようにコンフリクトする。  
  
```swift
print("Main!")
<<<<<<< HEAD
print("Main!!")
=======
print("Secondary!!")
>>>>>>> secondary

```
  
今回みたいに軽微な修正なら手作業で事足りるが、「どちらかのブランチを優先したい」時がある。  
  
その際に下記のコマンドが有効。  
  
## git checkout --own .  
コンフリクト箇所を master ブランチ（被マージブランチ）で解消。  
つまり、 master ブランチを優先してコンフリクトを解消する。  
  
```swift
print("Main!")
print("Main!!")
```
  
## git checkout --their .  
コンフリクト箇所を secondary ブランチ（マージ元ブランチ）で解消。   
つまり、secondary ブランチを優先してコンフリクトを解消する。  
  
```swift
print("Main!")
print("Secondary!!")
```
  
## こばなし  
コンフリクトがXcode上で起こった場合、一旦アプリを終了させることをお勧めします。  
Xcodeはディレクトリの構成などが変わったタイミングで自動で構成を再編成してくれているらしく、普段は便利ですがこういうときに予期せぬ差分を生み出す可能性があります。  