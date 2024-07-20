---
title: "Go でリバースプロキシを試す"
date: 2021-04-25T18:22:41+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/go.png"
tags:
- golang
---
  
ネットワーク系、手を動かして覚えていきたい。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> go version
go version go1.16 darwin/amd64
```
  
## 最小構成
  
localhost:8080 のリクエストを localhost:8081 に流す。  
  
#### サーバー (localhost:8080)
  
```go
package main

import (
	"log"
	"net/http"
	"net/http/httputil"
)

func main() {
	director := func(request *http.Request) {
		request.URL.Scheme = "http"
		request.URL.Host = ":8081"
	}

	rp := &httputil.ReverseProxy{Director: director}

	server := http.Server{
		Addr:    ":8080",
		Handler: rp,
	}

	err := server.ListenAndServe()
	if err != nil {
		log.Fatal(err.Error())
	}
}
```
  
#### サーバー (localhost:8081)
  
```go
package main

import (
	"log"
	"net/http"
)

func main() {
	server := http.Server{
		Addr:    ":8081",
		Handler: http.HandlerFunc(hander),
	}
	server.ListenAndServe()
}

func hander(w http.ResponseWriter, r *http.Request) {
	log.Println("hoge")
}
```
  
## リバースプロキシ経由でしかリクエストを受け付けない
  
リバースプロキシ経由 = リクエスト元が自分 = 127.0.0.1  
  
#### サーバー (localhost:8081)
  
```go
package main

import (
	"fmt"
	"net/http"
	"strings"
)

func main() {
	server := http.Server{
		Addr:    ":8081",
		Handler: http.HandlerFunc(hander),
	}
	server.ListenAndServe()
}

func hander(w http.ResponseWriter, r *http.Request) {
	if remoteIP := strings.Split(r.RemoteAddr, ":"); !isSelfIP(remoteIP[0]) {
		fmt.Fprintln(w, "リモートプロキシ経由以外でのアクセス")
		return
	}

	fmt.Fprintln(w, "リモートプロキシ経由でのアクセス")
}

func isSelfIP(ip string) bool {
	return ip == "127.0.0.1"
}
```
  
　
  
```bash
> curl localhost:8081
リモートプロキシ経由以外でのアクセス
  
> curl localhost:8080 
リモートプロキシ経由でのアクセス
```
  
## 参考  
  
- [http - The Go Programming Language](https://golang.org/pkg/net/http/)  
- [fmt - The Go Programming Language](https://golang.org/pkg/fmt/)  
  