---
title: "制約をOutlet接続して要素の表示を管理する"
date: 2020-07-23T00:06:34+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
制約をOutlet接続してみる。  
<!--more-->  
  
## 制約をOutlet接続  
通常のUIパーツのOutlet接続と同様に行う。  
  
![](./1.png)  
  
![](./2.png)  
  
## 条件分岐で制約を変更  
  
## 事前準備  
Storyboard上でUIStackViewを配置。  
中にUIViewを2つ格納し、片方の width を0にする。  
  
## ベース  
  
```swift
import UIKit

class ViewController: UIViewController {
    
    @IBOutlet weak var redView: UIImageView!
    @IBOutlet weak var redViewWidthConstraints: NSLayoutConstraint!
    
    let redFlag: Bool = false
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        if redFlag {
            redViewWidthConstraints.constant = CGFloat(100)
        }
    }
}
```
  
![](./4.png)  
  
## コード上から制約を変更  
`Bool` の変数の値に応じて `redView` の width を変更。

```swift
import UIKit

class ViewController: UIViewController {
    
    @IBOutlet weak var redView: UIImageView!
    @IBOutlet weak var redViewWidthConstraints: NSLayoutConstraint!
    
    let redFlag: Bool = true
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        if redFlag {
            redViewWidthConstraints.constant = CGFloat(100)
        }
    }
}
```
  
![](./3.png)  
  
## 所感  
  
実装の幅が広がるなあ。  
  