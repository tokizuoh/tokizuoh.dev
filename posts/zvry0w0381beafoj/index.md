---
title: "Firebase Analyticsでログ取得"
date: 2020-08-08T21:20:55+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
ログ取るの大事。  
<!--more-->  
  
## 開発環境  
  
```bash
Xcode: Version 11.5 (11E608c)  
Apple Swift version 5.2.4 (swiftlang-1103.0.32.9 clang-1103.0.32.53)
Target: x86_64-apple-darwin19.5.0
```
  
## セットアップ  
  
[Firebase を iOS プロジェクトに追加する](https://firebase.google.com/docs/ios/setup)  
  
[ステップ 5: アプリで Firebase を初期化する](https://firebase.google.com/docs/ios/setup#initialize-firebase) にて、 import しているのは `Firebase` だが、手元で `FirebaseApp` が使えなかった。  
`FirebaseCore` に変更したところ正常に動作することが確認できた。  
  
```swift
import FirebaseCore
```
  
## ログ取得処理を埋め込む  
  
```swift
import FirebaseAnalytics

// 省略

@IBAction func one(_ sender: Any) {
    // UIButton に紐づく処理

    // ログ取得処理の埋め込み
    Analytics.logEvent(AnalyticsEventScreenView, parameters: [
        AnalyticsParameterItemID: "button-1",
        AnalyticsParameterItemName: "button-1",
        AnalyticsParameterContentType: "cont"
    ])
}
```
  
UIButton押下時のログ取得処理を埋め込みたい場合、上記のように記述する。  
  
## ログ取得の確認  
[DebugView](https://console.firebase.google.com/project/_/analytics/debugview?hl=ja) で1分毎にログ取得の確認ができる。  
  
## 参考  
- [Firebase を iOS プロジェクトに追加する](https://firebase.google.com/docs/ios/setup#swift_1)
- [iOS 用 Google アナリティクスを使ってみる  |  Firebase](https://firebase.google.com/docs/analytics/ios/start)