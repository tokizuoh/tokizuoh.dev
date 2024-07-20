---
title: "Go製APIをSwiftから利用する"
date: 2021-02-28T19:06:52+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/go_swift.png"
tags:
- golang
- Swift
---
  
GoでAPIサーバーを立てて、Swiftから利用してみた。  
  
<!--more-->  
  
## お題  
  
```sql
mysql> select * from user;
+------+-----------+
| id   | name      |
+------+-----------+
|    1 | Akira     |
|    2 | Suzuki    |
|    0 | Takeshita |
+------+-----------+
```
  
- ローカルのMySQLに保存してあるデータをAPIで取得できるようにする (Go)  
- APIをiOSアプリから使えるようにする (Swift)  
  
## 開発環境  
  
```bash
> go version
go version go1.14.4 darwin/amd64

> xcodebuild -version
Xcode 12.3
Build version 12C33
```
  
## Go (APIサーバー)  
  
```bash
# go.mod

# ...

go 1.14

require github.com/go-sql-driver/mysql v1.5.0
```
  
　
  
```go
// server.go

package main

import (
	"database/sql"
	"encoding/json"
	"net/http"

	_ "github.com/go-sql-driver/mysql"
)

type User struct {
	Id   int    `json:"id"`
	Name string `json:"name"`
}

func fetchUser(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Only GET is allowed", http.StatusNotImplemented)
		return
	}

	cnn, err := sql.Open("mysql", "root:@/gotest")
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
	}

	rows, err := cnn.Query("select * from user")
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
	}

	var users []User
	for rows.Next() {
		var id int
		var name string
		err = rows.Scan(&id, &name)
		if err != nil {
			http.Error(w, err.Error(), http.StatusInternalServerError)
			return
		}
		users = append(users, User{id, name})
	}

	res, err := json.Marshal(users)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	w.Header().Set("Content-Type", "application/json")
	w.Write(res)
}

func main() {
	http.HandleFunc("/v1/user", fetchUser)
	http.ListenAndServe(":8080", nil)
}

```
  
## Swift (API利用)  
  
```swift
// ViewController.swift
import UIKit

struct User: Codable {
    let id: Int
    let name: String
}

private enum GoClientError: Error {
    case invalidPath
    case decodedFailure
    case unknown
}

private protocol APIClient {
    var basePath: String { get }
    var version: String { get }
}

private struct GoClient: APIClient {
    
    let basePath = "http://192.168.x.x:8080"
    let version = "v1"
    
    func fetchUsers(completion: @escaping (Result<[User], GoClientError>) -> Void) {
        let endpoint = "user"
        let path = "\(basePath)/\(version)/\(endpoint)"
        
        guard let url = URL(string: path) else {
            completion(.failure(.invalidPath))
            return
        }
        
        var request = URLRequest(url: url)
        request.httpMethod = "GET"
        
        let task = URLSession.shared.dataTask(with: request) { (data, urlResponse, error) in
            if error != nil {
                completion(.failure(.unknown))
            }
            
            if let data = data {
                do {
                    let users = try JSONDecoder().decode([User].self, from: data)
                    completion(.success(users))
                } catch {
                    completion(.failure(.decodedFailure))
                }
            }
        }
        task.resume()
    }
}

final class ViewController: UIViewController {

    var users: [User]? {
        didSet {
            guard let users = users else {
                return
            }
            
            let usersSortedById = users.sorted { (l, r) -> Bool in
                return l.id < r.id
            }

            for user in usersSortedById {
                print("ID-\(String(format: "%02d", user.id)): \(user.name)")
            }
        }
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
    
        let client = GoClient()
        client.fetchUsers { result in
            switch result {
            case .success(let users):
                self.users = users
                
            case .failure(let error):
                switch error {
                case .decodedFailure:
                    // エラーハンドリング
                    break
                case .invalidPath:
                    // エラーハンドリング
                    break
                case .unknown:
                    // エラーハンドリング
                    break
                }
            }
        }
    }
}

```
  
## 実行結果
  
```bash
ID-00: Takeshita
ID-01: Akira
ID-02: Suzuki
```
  
Xcode上のターミナルでMySQLのデータを出力できた。  
  
以下感想。  
  
---
  
## Go: APIを提供する関数内の処理の切り分け  
  
```go
func fetchUser(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Only GET is allowed", http.StatusNotImplemented)
		return
	}

	// MySQLとの接続
	cnn, err := sql.Open("mysql", "root:@/gotest")
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
	}

	// SQL構文の実行
	rows, err := cnn.Query("select * from user")
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
	}

	// 1レコードずつデータをコード内に落とし込む
	var users []User
	for rows.Next() {
		var id int
		var name string
		err = rows.Scan(&id, &name)
		if err != nil {
			http.Error(w, err.Error(), http.StatusInternalServerError)
			return
		}
		users = append(users, User{id, name})
	}

	// JSON([]byte)に変換
	res, err := json.Marshal(users)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	w.Header().Set("Content-Type", "application/json")
	w.Write(res)
}
```
  
本コードでは、`fetchUser()` 内で下記の処理が存在する。  
  
- MySQLとの接続
- SQL構文の実行
- 1レコードずつデータをコード内に落とし込む
- JSONに変換
  
「MySQLとの接続」は別関数で切り分けたほうが機能の切り分けとして良さそう。  
  
## Swift: URLSessionを用いたGetリクエスト  
  
処理待ちをどうするかで悩んだ。  
  
- DispatchGroup
- DispatchSemaphore
- completionHandler （本コード）
  
今後、処理待ち周りで良さそうなサードパーティのライブラリを見てみる。  
  
## 参考  
  
- [http - The Go Programming Language](https://golang.org/pkg/net/http/)  
- [json - The Go Programming Language (Marshal)](https://golang.org/pkg/encoding/json/#Marshal)  
  