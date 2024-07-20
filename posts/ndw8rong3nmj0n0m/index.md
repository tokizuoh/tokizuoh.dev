---
title: "goroutine を試す"
date: 2021-05-02T19:42:51+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/docker_go.png"
tags:
- golang
- docker
---
  
ついでに Docker を使った環境構築をメモ。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> docker --version
Docker version 19.03.12, build 48a66213fe

> docker-compose --version
docker-compose version 1.27.2, build 18f557f9

> go version
go version go1.16.3 linux/amd64
```
  
## goroutine  
  
> goroutine (ゴルーチン)は、Goのランタイムに管理される軽量なスレッドです。  
> [A Tour of Go](https://go-tour-jp.appspot.com/concurrency/1)  
  
- goroutine はスレッド
- 関数呼び出しの前に go を付与すると、新しい goroutine が実行される
- 新しい goroutine は呼び出し元の goroutine と同時に実行される
  
## 例1
A Tour of Go のコードを少し改良。  
  
```go
package main

import (
	"fmt"
	"time"
)

func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(i, s)
	}
}

func main() {
	go say("world")
	say("hello")
}
```
  
　
  
```bash
> go run main.go
0 world
0 hello
1 hello
1 world
2 world
2 hello
3 hello
3 world
4 world
4 hello
```
  
　
  
```bash
> go run main.go
0 world
0 hello
1 hello
1 world
2 world
2 hello
3 hello
3 world
4 hello
# 4 world が出力されない
```
  
　
  
何回か実行すると `"4 world"` が出力されないことがある。  
  
`"~ world"` の出力は main 関数とは別の goroutine で実行されている。  
main 関数の終了タイミングは main 関数と同じ goroutine で実行される `"~ hello"` の最後（`"4 hello"`）の処理が終わったタイミングなので、main 関数の goroutine の最後の処理が終わる前に他の goroutine の処理が終わっていなければそこで打ち切られてしまう。  
  
　
  
```go
package main

import (
	"fmt"
	"time"
)

func say(s string, num time.Duration) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * num * time.Millisecond)
		fmt.Println(i, s)
	}
}

func main() {
	go say("world", 100)
	say("hello", 1)
}
```
  
　
  
```bash
> go run main.go
0 hello
1 hello
2 hello
3 hello
4 hello
```
  
`time.Sleep` の重さを変えて実行したら main 関数とは別の goroutine で実行される処理が打ち切られてることが分かる。  
  
## 例2
main 関数とは別の goroutine の処理が終わるまで待つ。  
  
```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var wg = sync.WaitGroup{}

func say(s string) {
	defer wg.Done()
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(i, s)
	}
}

func main() {
	wg.Add(2)
	go say("world")
	say("hello")
	wg.Wait()
}

```
  
　
  
```bash
> go run main.go
0 world
0 hello
1 hello
1 world
2 world
2 hello
3 hello
3 world
4 hello
4 world
```
  
sync パッケージの [WaitGroup](https://golang.org/pkg/sync/#WaitGroup) を使って goroutine を同期的に実行した。  
  
　
  
sync.WaitGroup{} がグローバル変数なのが少し気になるので main に収まるようにクロージャを使って書き直した。  
  
```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func say(s string, completion func()) {
	defer completion()
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(i, s)
	}
}

func main() {
	wg := sync.WaitGroup{}
	wg.Add(2)
	go say("world", func() {
		wg.Done()
	})
	say("hello", func() {
		wg.Done()
	})

	wg.Wait()
}

```
  
---
  
## Docker で Go の環境構築  
  
#### Dockerfile
  
```dockerfile
FROM golang:1.16-alpine
WORKDIR /go/src

COPY ./ ./
RUN go mod download
```
  
#### docker-compose.yml
  
```yml
version: '3.8'
services:
    app:
        build: .
        tty: true
        ports:
            - 8080:8080
        volumes:
            - .:/go/src
```
  
#### コマンド
  

  
```bash
# コンテナの構築・起動
# -d: detached コンテナをバックグラウンドで起動（このオプションが無いとターミナルが専有される）
> docker-compose up -d

# main.go の実行
> docker-compose exec app go run main.go

# コンテナに潜る
> docker-compose exec app /bin/sh  
```
  
　
  
ホスト上の相対パスをコンテナにマウントしているので、ホストで編集→コンテナで実行がホスト上で完結する。便利。   
  
## 参考  
  
- [A Tour of Go](https://go-tour-jp.appspot.com/concurrency/1)  
- [Go by Example: Goroutines](https://gobyexample.com/goroutines)  
- [sync - The Go Programming Language](https://golang.org/pkg/sync/#WaitGroup)  
- [up — Docker-docs-ja 19.03 ドキュメント](https://docs.docker.jp/compose/reference/up.html)  
- [Compose ファイル・リファレンス — Docker-docs-ja 19.03 ドキュメント](https://docs.docker.jp/compose/compose-file.html#volumes-volume-driver)  
  