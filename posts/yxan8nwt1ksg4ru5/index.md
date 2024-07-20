---
title: "Go で容量の大きい CSV ファイルを分割して処理する"
date: 2021-05-15T23:12:09+09:00
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
  
## やること
  
- 容量の大きい CSV ファイル（レコード数 45000）に対して下記処理を行う
    - 処理
        1. CSV 読み込み
        2. 各レコードに対して、空文字判定と重複判定  
        3. CSV 書き込み
- 読み込みと書き込みにおける、レコードの順序は整合性は取らない
  
#### CSV ファイル  
  
```bash
raw,roman
頭金,atamakin
大砲,taihou
溢れでる,ahurederu
検閲官,kenetukan
吃逆,syakkuri
.
.
.
```
  
## 基本形  
  
```go
package main

import (
	"encoding/csv"
	"io"
	"log"
	"os"
	"time"
)

func processing(lines [][]string) [][]string {
	var r [][]string
	set := make(map[string]struct{})
	for _, line := range lines {
		if line[1] == "" {
			continue
		}
		if _, exist := set[line[0]]; exist {
			continue
		}
		set[line[0]] = struct{}{}
		r = append(r, line)
	}
	return r
}

func main() {
	// [start] measure time
	start := time.Now()

	// read csv
	inputName := "debug_roman.csv"
	file, err := os.Open(inputName)
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()
	reader := csv.NewReader(file)

	isHeader := true
	var header []string
	var lines [][]string
	for {
		line, err := reader.Read()
		if err == io.EOF {
			break
		}
		if err != nil {
			log.Fatal(err)
		}
		if isHeader {
			header = line
			isHeader = false
			continue
		}
		lines = append(lines, line)
	}

	outputLines := processing(lines)

	// write csv
	outputName := "output_" + inputName
	file, err = os.OpenFile(outputName, os.O_WRONLY|os.O_CREATE, 0600)
	if err != nil {
		log.Fatal(err)
	}

	writer := csv.NewWriter(file)
	writer.Write(header)
	writer.WriteAll(outputLines)

	// [end] measure time
	log.Printf("elapsed: %fs", time.Since(start).Seconds())
}
```
  
　
  
```bash
> go run main.go
2021/05/15 14:19:24 elapsed: 0.079805s
```
  
## goroutine で処理を並列実行  
  
```go
package main

import (
	"encoding/csv"
	"io"
	"log"
	"os"
	"time"
)

func processing(lines [][]string, w *csv.Writer) {
	var r [][]string
	set := make(map[string]struct{})
	for _, line := range lines {
		if line[1] == "" {
			continue
		}
		if _, exist := set[line[0]]; exist {
			continue
		}
		set[line[0]] = struct{}{}
		r = append(r, line)
	}
	w.WriteAll(r)
}

func main() {
	// [start] measure time
	start := time.Now()

	// 一つの goroutine で処理するレコード数
	v := 1000

	// read csv
	inputName := "debug_roman.csv"
	file, err := os.Open(inputName)
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()
	
	reader := csv.NewReader(file)

	header, err := reader.Read()
	if err != nil {
		log.Fatal(err)
	}

	// write csv
	outputName := "output_" + inputName
	file, err = os.OpenFile(outputName, os.O_WRONLY|os.O_CREATE, 0600)
	if err != nil {
		log.Fatal(err)
	}
	writer := csv.NewWriter(file)
	writer.Write(header)

	isEOF := false
	for {
		var lines [][]string
		for i := 0; i < v; i++ {
			line, err := reader.Read()
			if err == io.EOF {
				isEOF = true
				break
			}
			if err != nil {
				log.Fatal(err)
			}
			lines = append(lines, line)
		}

		go processing(lines, writer)

		if isEOF {
			break
		}
	}

	// [end] measure time
	log.Printf("elapsed: %fs", time.Since(start).Seconds())
}

```
  
　
  
```bash
> go run main.go
2021/05/15 14:47:41 elapsed: 0.031551s
```
  
`0.079805s` から `0.031551s` と実行速度は2倍程度に高速化された。  
  
## 所感
  
- 雑に goroutine で処理を行ったけどうまくいった
    - goroutine の数についての記事が結構ヒットしたので、数は管理する必要がありそう（今回はたまたまうまくいっただけ）
- 変数名に迷いを感じた
  
## 参考  
  
- [Go maps in action - The Go Blog](https://blog.golang.org/maps)  
- [ルビ振り - Yahoo!デベロッパーネットワーク](https://developer.yahoo.co.jp/webapi/jlp/furigana/v1/furigana.html)  
  