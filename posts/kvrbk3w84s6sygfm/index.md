---
title: "io.ReadAll, io.ReadAtLeast, ReadFull を試す"
date: 2021-08-03T20:29:50+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/go.png"
tags:
- golang
---
  
Read系多い。  
  
<!--more-->  
  
基本的に [io · pkg.go.dev](https://pkg.go.dev/io) のコードを見ていく。  
  
## 開発環境  
  
```bash
> docker-compose exec app go version
go version go1.16.3 linux/amd64

> docker --version
Docker version 19.03.12, build 48a66213fe

> docker-compose --version
docker-compose version 1.27.2, build 18f557f9
```
  
## io.ReadAll
  
```go
package main

import (
	"io"
	"log"
	"strings"
)

func main() {
	r := strings.NewReader("AbCdE")

	b, err := io.ReadAll(r)
	if err != nil {
		log.Fatal(err)
	}

	// AbCdE
	log.Printf("%s", b)
}
```
  
エラー or EOF まで読み取ったデータ(`[]byte`)を返す。  
読み取り成功時の戻り値の err には EOF ではなく、nil が入る。  
  
## Seek した後に io.ReadAll
  
```go
package main

import (
	"io"
	"log"
	"strings"
)

func main() {
	r := strings.NewReader("AbCdE")
	_, err := r.Seek(2, io.SeekStart)
	if err != nil {
		log.Fatal(err)
	}

	b, err := io.ReadAll(r)
	if err != nil {
		log.Fatal(err)
	}

	// CdE
	log.Printf("%s", b)
}

```
  
`r.Seek(2, io.SeekStart)` を使って、読み込み対象の offset を先頭を基準に2つ後ろにずらしてから、 ReadAll を使った。  
  
"All" は r (`Reader`) にかかっており、受け取った時点の r の全てを読み込む。  
  
## ReadAtLeast
  
```go
package main

import (
	"io"
	"log"
	"strings"
)

func main() {
	r := strings.NewReader("AbCdE")

	buf := make([]byte, 3)
	if _, err := io.ReadAtLeast(r, buf, 1); err != nil {
		log.Fatal(err)
	}

	// AbC
	log.Printf("%s", buf)
}
```
  
少なくとも最小バイト( 3 番目の引数) を読み取るまで、r から buf に読み取る。  
  
#### err: short buffer
  
```go
package main

import (
	"io"
	"log"
	"strings"
)

func main() {
	r := strings.NewReader("AbCdE")

	shortBuf := make([]byte, 1)
	if _, err := io.ReadAtLeast(r, shortBuf, 3); err != nil {
		// short buffer
		log.Fatal(err)
	}

	// 到達しない
	log.Printf("%s", shortBuf)
}

```
  
r から読み取った長さに対し、用意したスライスの長さが短いときに発生するエラー。  
  
#### err: unexpected EOF
  
```go
package main

import (
	"io"
	"log"
	"strings"
)

func main() {
	r := strings.NewReader("AbCdE")

	longBuf := make([]byte, 2048)
	if _, err := io.ReadAtLeast(r, longBuf, 1024); err != nil {
		// unexpected EOF
		log.Fatal(err)
	}

	// 到達しない
	log.Printf("%s", longBuf)
}

```
  
r から min バイト未満しか読み込めなかった場合に発生するエラー。  
  
## ReadFull
  
```go
package main

import (
	"io"
	"log"
	"strings"
)

func main() {
	r := strings.NewReader("AbCdE")

	buf := make([]byte, 3)
	if _, err := io.ReadFull(r, buf); err != nil {
		log.Fatal(err)
	}

	// AbC
	log.Printf("%s\n", buf)
}

```
  
r から len(buf) バイトを buf に読み込む。  
ReadAtLeast は明示的に読み込むバイト数を引数で渡していたのに対し、ReadFull は読み込み先の `[]byte` のみ渡している。  
  
#### error: unexpected EOF
  
```go
package main

import (
	"io"
	"log"
	"strings"
)

func main() {
	r := strings.NewReader("AbCdE")

	buf := make([]byte, 11)
	if _, err := io.ReadFull(r, buf); err != nil {
		// unexpected EOF
		log.Fatal(err)
	}

	// 到達しない
	log.Printf("%s\n", buf)
}

```
  
読み取れるバイト数を読み込んだ後に EOF が発生した場合に発生するエラー。  
読み込み対象以上のバイト数の `[]byte` を引数として渡した場合に発生する。  
  
## 参考  
  
- [io · pkg.go.dev](https://pkg.go.dev/io)  
  