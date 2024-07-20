---
title: "database/sqlパッケージを試す"
date: 2021-02-27T21:51:13+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/go.png"
tags:
- golang
- sql
---
  
iOSアプリ開発でDB周りを扱う必要が出てきたので、SQLに入門する。  
  
<!--more-->  
  
せっかくなので、普段使用しているSwift以外の言語、GoでSQLを扱ってみる。  
まず、MySQLで基本となるSQL構文を使っていく。  
  
## 開発環境  
  
```bash
> go version
go version go1.15.5 darwin/amd64

> mysql --version
mysql  Ver 8.0.23 for osx10.15 on x86_64 (Homebrew)
```
  
## MySQL を使ってみる  
  
### bash
  
```bash
# MySQLの起動/停止
mysql.server start
mysql.server stop

# MySQLに潜り込む
mysql -u root
```
  
　
  
### sql
  
```sql
/*! データベース作成 */
mysql> create database go-test;

/*! データベースに潜る */
mysql> use gotest;

/*! テーブル表示 */
mysql> show tables;

/*! テーブル追加 */
mysql> create table user(id int, name varchar(10));

/*! テーブルにレコードを追加 */
mysql> insert into user values (1, "Akira");

/*! テーブルの中身確認 */
mysql> select * from user;
+------+-------+
| id   | name  |
+------+-------+
|    1 | Akira |
+------+-------+

/*! ~レコード追加~ */
mysql> select * from user;
+------+-----------+
| id   | name      |
+------+-----------+
|    1 | Akira     |
|    2 | Suzuki    |
|    0 | Takeshita |
+------+-----------+

/*! テーブルの中身確認 (idでソート) */
mysql> select * from user order by id;
+------+-----------+
| id   | name      |
+------+-----------+
|    0 | Takeshita |
|    1 | Akira     |
|    2 | Suzuki    |
+------+-----------+
```
  
## database/sql を使ってみる
とりあえず、`select * from user`。  
  
```go
package main

import (
	"database/sql"
	"fmt"

	_ "github.com/go-sql-driver/mysql"
)

func main() {
	cnn, err := sql.Open("mysql", "root:@/gotest")  // 試す目的なのでrootで
	if err != nil {
		panic(err.Error())
	}

	var id int
	var name string
	err = cnn.QueryRow("select * from user").Scan(&id, &name)
	if err != nil {
		panic(err.Error())
	}
	fmt.Println(id, name)
}
```
  
　
  
```bash
> go run main.go
1 Akira
```
  
あれ、`select * from user` なのに1つしかレコードを取得できていない。  
`QueryRow()` の[ドキュメント](https://golang.org/pkg/database/sql/#DB.QueryRow)を見る。  
  
> QueryRow executes a query that is expected to return at most one row. 
  
そもそも、 `QueryRow` は最大1レコードしか返さないメソッドだった。  
  
　
  
全レコードを取得したいので、`Query()`を試す。  
  
```go
package main

import (
	"database/sql"
	"fmt"

	_ "github.com/go-sql-driver/mysql"
)

func main() {
	cnn, err := sql.Open("mysql", "root:@/gotest")
	if err != nil {
		panic(err.Error())
	}

	rows, err := cnn.Query("select * from user")
	if err != nil {
		panic(err.Error())
	}

	for rows.Next() {
		var id int
		var name string
		err = rows.Scan(&id, &name)
		if err != nil {
			panic(err.Error())
		}
		fmt.Println(id, name)
	}
}
```
  
　
  
```bash
> go run main.go
1 Akira
2 Suzuki
0 Takeshita
```
  
全レコード取得できた。  
  
  
## 参考  
  
- [sql - The Go Programming Language](https://golang.org/pkg/database/sql/)  
- [MySQL 入門 - Qiita](https://qiita.com/okamuuu/items/c4efb7dc606d9efe4282)  
  