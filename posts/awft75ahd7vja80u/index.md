---
title: "traitCollectionDidChange(_:) は明るさ切り替え以外にも呼ばれる"
date: 2021-11-13T15:45:00+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
明るさ切り替え時の用途以外で使ったことがなかったので詳しく見てみる！  
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 13.1
Build version 13A1030d
```
  
## traitCollectionDidChange(_:) とは
  
https://developer.apple.com/documentation/uikit/uitraitenvironment/1623516-traitcollectiondidchange
  
**iOS interface environment** が変更されたときに呼ばれる。  
  
## iOS interface environment とは
  
[UITraitEnvironment](https://developer.apple.com/documentation/uikit/uitraitenvironment) によると、以下が該当する。  
  
- 水平・垂直のSize Class
- 表示されるスケール
- user interface idiom
  
traitCollectionDidChange(_:) が上記に変更があったときに呼ばれるか確認する。  
  
## 水平・垂直のSize Classの変更をハンドリング
  
水平・垂直の Size Class の変更があったときに traitCollectionDidChange(_:) が呼ばれるか確認する。  
  
```swift
import UIKit

final class AccessibilityViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
    }

    override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
        super.traitCollectionDidChange(previousTraitCollection)
        guard let previousTraitCollection = previousTraitCollection else {
            return
        }

        let hasChangeVerticalSizeClass = previousTraitCollection.verticalSizeClass != traitCollection.verticalSizeClass
        let hasChangeHorizontalSizeClass = previousTraitCollection.horizontalSizeClass != traitCollection.horizontalSizeClass
        if hasChangeVerticalSizeClass || hasChangeHorizontalSizeClass {
            print("Size Class 変わったよ！")
            // ここで取れる view.frame は回転後の値
        }
    }

}
```
  
　
  
画面回転時に、Size Class に変更がある場合のみ呼ばれた。  
画面回転したからといって必ず呼ばれるわけではない。  
  
iPad は Vertical も Horizonral も Regular なので、iPad では画面回転時に呼ばれない。  
  
## 表示されるスケールの変更をハンドリング
  
ハンドリングは無理そう。  
  
[displayScale](https://developer.apple.com/documentation/uikit/uitraitcollection/1623519-displayscale) はRetinaディスプレイかどうかの値が格納されている。  
0.0: 指定なし、1.0: 非Retina、2.0: Retina。  
  
アプリ実行中にこの値が変えようが無い→ハンドリングも出来ない、のでスルー。  
  
~~（念の為、[質問](https://forums.swift.org/t/does-the-displayscale-of-the-traitcollection-change-while-the-app-is-running/53465)中）~~ 再現方法模索中。  
  
## user interface idiom の変更をハンドリング  
  
[UIUserInterfaceIdiom](https://developer.apple.com/documentation/uikit/uiuserinterfaceidiom) はデバイスまたは View や ViewController などの trait environment を持つオブジェクトのインターフェイスタイプを示す定数。  
定義を見ると列挙型で、アプリが実行される媒体（iPhone, iPad, tvOS, ...）の種別が格納される。  
  
これもアプリ起動中に変わりようがないので変更のハンドリングは無理そう。  
  
## まとめ
  
traitCollectionDidChange(_:) は明るさ切り替え以外に、画面回転時にSize Classに変更があった時に呼ばれる。  
  
## 参考  
  
- [traitCollectionDidChange(_:) | Apple Developer Documentation](https://developer.apple.com/documentation/uikit/uitraitenvironment/1623516-traitcollectiondidchange)  
- [UITraitEnvironment | Apple Developer Documentation](https://developer.apple.com/documentation/uikit/uitraitenvironment)  
- [displayScale | Apple Developer Documentation](https://developer.apple.com/documentation/uikit/uitraitcollection/1623519-displayscale)  
- [UIUserInterfaceIdiom | Apple Developer Documentation](https://developer.apple.com/documentation/uikit/uiuserinterfaceidiom)  
  