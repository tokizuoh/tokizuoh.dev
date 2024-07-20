---
title: "goroutineでGoの非同期処理を試す"
date: 2021-02-11T22:07:06+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/go.png"
tags:
- golang
---
  
[A Tour of Go](https://go-tour-jp.appspot.com/concurrency/1) のコードを少し変えて動きを見ていく。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> go version
go version go1.15.5 darwin/amd64
```
  
## goroutine  
  
```go
package main

import (
    "fmt"
    "time"
)

func say(s string) {
    for i := 0; i < 3; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
}

func main() {
    say("1")
    say("Two")
}

```
  
　
  
```bash
> go run main.go
1
1
1
Two
Two
Two
```
  
同期処理なので`say`が順次実行されている。  
  
　
  
```go
package main

import (
    "fmt"
    "time"
)

func say(s string) {
    for i := 0; i < 3; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
}

func main() {
    go say("1")  // goを追加
    say("Two")
}
```
  
　
  
```bash
> go run main.go
Two
1
1
Two
1
Two
```
  
（実行結果はあくまで一例。スレッドの状況によって実行順序が変わる。）  
`say("1")`が非同期で実行された。  
  
## goroutine内での値変更
  
```go
package main

import (
    "fmt"
    "time"
)

var g int

func change(num int) {
    time.Sleep(100 * time.Millisecond)
    g = num
}

func main() {
    change(1)
    go change(2)
    fmt.Println(g)  // 1 (2が実行される前にだいたい到達する)

    time.Sleep(100 * time.Millisecond)
    fmt.Println(g)  // 1 or 2 (スレッドの状況によって実行順序が変わる)
}
```
  
この方法だと非同期処理で値を変えるのはブレがあるのでよくなさそう。  
## sync.WaitGroup  
  
SwiftでいうところのDispatchSemaphore。  
  
```go
package main

import (
    "fmt"
    "sync"
    "time"
)

var g int

func change(num int) {
    time.Sleep(100 * time.Millisecond)
    g = num
}

func main() {
    var wg sync.WaitGroup
    for i := 0; i < 5; i++ {
        wg.Add(1)
        go func(j int) {
            change(i)
            defer wg.Done()
        }(i)
    }
    fmt.Println(g)  // 即時実行されるので0(ゼロ値)
    wg.Wait()
    fmt.Println(g)  // 0...5 (スレッドの状況によって実行順序が変わるが、高確率で5になる)
}
```
  
## 参考  
  
- [A Tour of Go](https://go-tour-jp.appspot.com/concurrency/1)  
  