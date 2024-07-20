---
title: "Moyaを試した"
date: 2020-08-10T22:08:22+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
  
SwiftのHTTP通信周りのライブラリの使い方を覚えていきたい。  
<!--more-->  
  
## Moya  
  
![](https://raw.githubusercontent.com/Moya/Moya/master/web/logo_github.png)  
([Moya/Moya: Network abstraction layer written in Swift.](https://github.com/Moya/Moya) より)  
  
内部的に[Alamofire](https://github.com/Alamofire/Alamofire)を使用しているネットワーク通信周りの抽象化ライブラリ。  
簡単な通信ならAlamofireと比べて、Moyaの方が取っつきやすそう。  
  
## 開発環境  
  
```bash
Xcode: Version 11.5 (11E608c)  
Apple Swift version 5.2.4 (swiftlang-1103.0.32.9 clang-1103.0.32.53)
Target: x86_64-apple-darwin19.5.0
```
  
## 最小構成で試運転  
[AtCoder Problems API / Datasets](https://github.com/kenkoooo/AtCoderProblems/blob/master/doc/api.md) から問題一覧を取得する。  
なるべく最小構成で書いたつもり。  
  
### モデル  
  
```bash
[
    {
        "id":"abc001_1",
        "contest_id":"abc001",
         "title":"A. 積雪深差"
    },
    {
        "id":"abc001_2",
        "contest_id":"abc001",
        "title":"B. 視程の通報"
    }, ...
]
```
  
[AtCoder Problems API / Datasets > Problems Information](https://kenkoooo.com/atcoder/resources/problems.json) は上記のようなレスポンスになっている。ここから下記のように構造体でモデルを作る。  
  
```swift
struct Problem: Codable {
    let id: String
    let contestId: String
    let title: String
    
    private enum CodingKeys: String, CodingKey {
        case id
        case contestId = "contest_id"
        case title
    }
}
```
  
Swiftの命名規則的に変数名は lower camel case なので、`contestId` が取得先の key（snake case）と合わない。  
そこで `CodingKey` を使って、エンコードとデコードのキーを一致させた。  
  
### 接続先のAPI情報  
  
```swift
import Moya

enum ProblemAPI {
    case problem
}

extension ProblemAPI: TargetType {
    var baseURL: URL {
        return URL(string: "https://kenkoooo.com")!
    }
    
    var path: String {
        switch self {
        case .problem:
            return "/atcoder/resources/problems.json"
        }
    }
    
    var method: Moya.Method {
        return Moya.Method.get
    }
    
    var sampleData: Data {
        return Data()
    }
    
    var task: Task {
        return .requestPlain
    }
    
    var headers: [String : String]? {
        return nil
    }
}
```
  
`TargetType` に準拠させて定義する。  
`path` (APIのエンドポイント)は enum で複数定義できる。今回は簡単のため、一つだけ定義した。  
  
## 呼び出す  
  
```swift
import UIKit
import Moya

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        
        let provider = MoyaProvider<ProblemAPI>()
        provider.request(.problem) { (result) in
            switch result {
            case .success(let response):
                let data = response.data
                let problems = try? JSONDecoder().decode([Problem].self, from: data)
                
                for problem in problems! {
                    print(problem.id, problem.contestId, problem.title)
                }
            case .failure(let error):
                print(error)
            }
        }
    }

}
```
  
```bash
abc001_1 abc001 A. 積雪深差
abc001_2 abc001 B. 視程の通報
abc001_3 abc001 C. 風力観測
abc001_4 abc001 D. 感雨時刻の整理
abc002_1 abc002 A. 正直者
abc002_2 abc002 B. 罠
abc002_3 abc002 C. 直訴
abc002_4 abc002 D. 派閥
...
```
  
実際にアプリに組み込む時はエラーハンドリングしよう。  
全体的にかなりスッキリ書けた。  
  
## 参考  
- [Moya/Moya: Network abstraction layer written in Swift.](https://github.com/Moya/Moya)
- [Alamofire/Alamofire: Elegant HTTP Networking in Swift](https://github.com/Alamofire/Alamofire)  
- [AtCoderProblems/api.md at master · kenkoooo/AtCoderProblems](https://github.com/kenkoooo/AtCoderProblems/blob/master/doc/api.md)  
- [MoyaでGithubAPIに通信する – 野生のプログラマZ](http://harumi.sakura.ne.jp/wordpress/2019/06/25/moya%E3%81%A7githubapi%E3%81%AB%E9%80%9A%E4%BF%A1%E3%81%99%E3%82%8B/)  
- [Codableについて色々まとめた[Swift4.x] - Qiita](https://qiita.com/_ha1f/items/bf1aad5ea3e927f59f9d)  