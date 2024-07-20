---
title: "recover"
date: 2021-05-23T09:51:59+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/go.png"
tags:
- golang
---
  
Go の recover、知らない概念だった。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> go version
go version go1.16.3 linux/amd64
```
  
## recover  
  
recover を検索すると、defer, panic と共に説明されることが多い。  
  
defer は普段使用している Swift に存在し、 panic も触った感じ [fatalError](https://developer.apple.com/documentation/swift/1538698-fatalerror) と同じような挙動をするため、漠然とは理解していたが、recover は初めての概念だったので調べた。  
  
> Recover is a built-in function that regains control of a panicking goroutine. Recover is only useful inside deferred functions. During normal execution, a call to recover will return nil and have no other effect. If the current goroutine is panicking, a call to recover will capture the value given to panic and resume normal execution.  
> [Defer, Panic, and Recover - The Go Blog](https://blog.golang.org/defer-panic-and-recover)  
  
- panic な goroutine の制御を取り戻す  
- defer 内のみ有効  
- panic な goroutine 内で recover を使うと、recover の呼び出し時に panic 時に与えた値をキャプチャする
  
　
  
```go
package main

import "log"

func f() {
	defer func() {
		if r := recover(); r != nil {
			log.Println("Recoverd in f", r)
		}
	}()
	panic("Panic!")
}

func main() {
	f()
}
```
  
　
  
```bash
> go run main.go
2021/05/23 01:00:31 Recoverd in f Panic!
```
  
panic 時に与えた値が recover にキャプチャされていることが分かった。  
  
## 参考  
  
- [Defer, Panic, and Recover - The Go Blog](https://blog.golang.org/defer-panic-and-recover)  
- [fatalError(_:file:line:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/1538698-fatalerror)  
  