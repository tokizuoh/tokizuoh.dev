---
title: "channel を試す"
date: 2021-05-03T19:43:23+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/go.png"
tags:
- golang
---
  
<!--more-->  
  
## 開発環境  
  
```bash
> go version
go version go1.16.3 linux/amd64
```
  
## channel
  
> チャネル( Channel )型は、チャネルオペレータの <- を用いて値の送受信ができる通り道です。  
> [A Tour of Go](https://go-tour-jp.appspot.com/concurrency/2)
  
- goroutine 間で値をやりとりする仕組み
  
## 例1
  
[Go by Example](https://gobyexample.com/channels) のコードを見る。  
  
```go
package main

import "fmt"

func main() {

	messages := make(chan string)

	// message channel に文字列 "ping" を送っている
	go func() { messages <- "ping" }()

	// 変数 msg に message channel の値( "ping" )が代入される
	msg := <-messages

	// "ping" が出力される
	fmt.Println(msg)
}

```
  
　
  
```bash
> go run main.go
ping
```
  
goroutine 間で値が渡されることが分かった。  
  
## 例2
  
```go
package main

import (
	"fmt"
	"time"
)

func main() {

	messages := make(chan string)

	// 即時実行されるが、①で messages channel に値が渡されるまで fmt.Println ができない（= ロックされる）のでブロック
	go func() {
		time.Sleep(10000 * time.Millisecond)
		fmt.Println(<-messages)
	}()

	// ①
	messages <- "Hello World"
}
```
  
　
  
```bash
> go run main.go
Hello World
```
  
　
  
> 通常、片方が準備できるまで送受信はブロックされます。これにより、明確なロックや条件変数がなくても、goroutineの同期を可能にします。  
> [A Tour of Go](https://go-tour-jp.appspot.com/concurrency/2)  
  
明確なロック = [WaitGroup](https://golang.org/pkg/sync/#WaitGroup)など  
  
## 参考  
  
- [A Tour of Go](https://go-tour-jp.appspot.com/concurrency/2)  
- [Go by Example: Channels](https://gobyexample.com/channels)  
  