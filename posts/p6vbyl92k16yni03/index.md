---
title: "GORMを試す"
date: 2021-03-06T20:11:53+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/go.png"
tags:
- golang
- sql
---
  
SQL構文は自分で書きたい感。  
<!--more-->  
  
## お題  
  
```sql
mysql> select * from users;
+------+----------+
| id   | name     |
+------+----------+
|    6 | Kanda    |
|    5 | Yoshioka |
|    4 | Piyo     |
+------+----------+
```
  
MySQL内のレコードを[GORM](https://github.com/go-gorm/gorm)を使って取得できるようにする  
  
## 開発環境  
  
```bash
> goenv global
1.15.5

> mysql --version
mysql  Ver 8.0.23 for osx10.15 on x86_64 (Homebrew)

# go.mod(抜粋)
require github.com/jinzhu/gorm v1.9.16
```
  
## GORM
  
[go-gorm/gorm: The fantastic ORM library for Golang, aims to be developer friendly](https://github.com/go-gorm/gorm)  
GORMはGoのORMライブラリ。  
  
## コード  
  
```go
package main

import (
	"fmt"

	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

// 構造体の命名はテーブル名(users)の単数形にする必要がある
type User struct {
	Id   int    `json:id`
	Name string `json:name`
}

func connectGorm() *gorm.DB {
	DBMS := "mysql"
	USER := "username"
	PASS := "password"
	PROTOCOL := "tcp(x.x.x.x:3306)"
	DBNAME := "database_name"

	CONNECT := fmt.Sprintf("%s:%s@%s/%s", USER, PASS, PROTOCOL, DBNAME)
	db, err := gorm.Open(DBMS, CONNECT)
	if err != nil {
		panic(err.Error())
	}
	return db
}

func main() {
	db := connectGorm()
	defer db.Close()

	var users []User
	if err := db.Find(&users).Error; err != nil {
		panic(err.Error())
	}

	fmt.Println(users)
}

```
  
　
  
```bash
> goenv exec go run main.go
[{6 Kanda} {5 Yoshioka} {4 Piyo}]
```
  
MySQL内のレコードを取得できた。  
  
## MySQL: ユーザーの作成
  
```sql
/*! ユーザー名: hoge, パスワード: fuga */
create user 'hoge' identified by 'fuga';
```
  
ユーザー名とパスワードは両方、クォーテーションで囲む必要がある。  
  
## 参考  
  
- [go-gorm/gorm: The fantastic ORM library for Golang, aims to be developer friendly](https://github.com/go-gorm/gorm)  
- [Query | GORM - The fantastic ORM library for Golang, aims to be developer friendly.](https://gorm.io/docs/query.html)  
  