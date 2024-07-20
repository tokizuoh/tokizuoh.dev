---
title: "computedプロパティの相互参照を防ぐ"
date: 2020-12-12T16:43:46+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
- expt
---
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 12.0.1
Build version 12A7300
```
  
## コード
  
```swift
import UIKit

struct Hoge {
    static var p1: Int {
        get {
            return p2  // Thread 1: EXC_BAD_ACCESS (code=2, address=0x...)
        }
    }
    static var p2: Int {
        get {
            return p1
        }
    }
}

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        print(Hoge.p1)
        // 以降は実行されない
    }
}
```
  
上記のようなコードはビルドはできるが実行時にエラーとなる。  
相互参照回数は52314回程度だった。  
  
　
  
```swift
import UIKit

struct Hoge {
    static var p1: Int {
        get {
            count += 1
            execFatalError(condition: count >= 5)
            return p2
        }
    }
    static var p2: Int {
        get {
            count += 1
            execFatalError(condition: count >= 5)
            return p1
        }
    }
    static var count = 0
}

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        print(Hoge.p1)
    
    }
}

func execFatalError(condition: Bool) {
    guard condition else {
        return
    }
    fatalError()
}
```
  
getter内に相互参照した回数で分岐させる処理を書けば良さそう。  
  