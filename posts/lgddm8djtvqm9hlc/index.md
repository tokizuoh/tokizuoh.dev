---
title: "Seek を試す"
date: 2021-07-31T21:31:00+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/go.png"
tags:
- golang
---
  
標準ライブラリのコードリーディングを始めた。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> docker-compose exec app go version
go version go1.16.3 linux/amd64

> docker --version
Docker version 19.03.12, build 48a66213fe

> docker-compose --version
docker-compose version 1.27.2, build 18f557f9
```
  
## Seek
  
```go
// Seek sets the offset for the next Read or Write on file to offset, interpreted
// according to whence: 0 means relative to the origin of the file, 1 means
// relative to the current offset, and 2 means relative to the end.
// It returns the new offset and an error, if any.
// The behavior of Seek on a file opened with O_APPEND is not specified.
//
// If f is a directory, the behavior of Seek varies by operating
// system; you can seek to the beginning of the directory on Unix-like
// operating systems, but not on Windows.
func (f *File) Seek(offset int64, whence int) (ret int64, err error) {
	if err := f.checkValid("seek"); err != nil {
		return 0, err
	}
	r, e := f.seek(offset, whence)
	if e == nil && f.dirinfo != nil && r != 0 {
		e = syscall.EISDIR
	}
	if e != nil {
		return 0, f.wrapErr("seek", e)
	}
	return r, nil
}
```
[go/file.go#Seek](https://github.com/golang/go/blob/master/src/os/file.go#L234)  
  
　
  
ドキュメント部分を翻訳すると、  
  

> Seekは、ファイルに対する次の Read または Write の offset を、whence に従って解釈される offset に設定します。0 はファイルの原点からの相対位置、1 は現在の offset からの相対位置、2 は終端からの相対位置を意味します。
  
- offset: 配列やデータ構造オブジェクト内の、先頭から所定の要素まで距離を示す整数
- whence: どこから
  
　
  
whence を 3パターン から設定できる。  
  
|  定数名 (実際の値)  |  whence の基準  |
| ---------------- | -------------- |
|  SeekStart (0)    |  先頭           |
|  SeekCurrent (1)  |  現在の offset |
|  SeekEnd (2)      |  終端           |
  
　
  
Seek はファイルを指定した位置から読ませることが得意そう。  
コードを書いてみる。  
  
## コード
  
テキストファイルの読み出し位置をコントロールして好きな位置で取り出せるようにする。  
  
```bash
> cat test.txt
Hello, World!
```
  
　
  
```go
package main

import (
	"fmt"
	"io"
	"io/ioutil"
	"log"
	"os"
)

func main() {
	f, err := os.Open("./test.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer f.Close()

	f.Seek(0, io.SeekStart)
	bytes, err := ioutil.ReadAll(f)
	if err != nil {
		log.Fatal()
	}

	fmt.Println("length:", len(bytes))
	fmt.Println(string(bytes))
}
```
  
↓
  
```bash
> docker-compose exec app go run main.go 
length: 13
Hello, World!
```
  
　
  
```go
f.Seek(0, io.SeekStart)
```
ここで、offset を先頭を基準として 0 に設定している。  
そのため読み出したテキストは全文表示される。（`Hello, World!`）
  
---
  
```go
f.Seek(5, io.SeekStart)
```
  
↓
  
```bash
> docker-compose exec app go run main.go 
length: 8
, World!
```
  
offset を先頭を基準として 5 に設定している。(= 先頭から 5 進めた箇所から読み込む)  
  
---
  
```go
f.Seek(0, io.SeekCurrent)
```
  
↓
  
```bash
> docker-compose exec app go run main.go 
length: 13
Hello, World!
```
  
offset を現在の offset を基準として 0 に設定している。(= offsetを変更していないので、先頭から読み込む)  
  
---
  
```go
f.Seek(-5, io.SeekEnd)
```
  
↓
  
```bash
> docker-compose exec app go run main.go 
length: 5
orld!
```
  
offset を終端を基準として -5 に設定している。(= 終端から 5 戻した箇所から読み込む)  
  
## 参考  
  
- [go/io.go at master · golang/go](https://github.com/golang/go/blob/master/src/io/io.go)  
- [go/io.go at master · golang/go](https://github.com/golang/go/blob/master/src/io/io.go)  
- [io · pkg.go.dev](https://pkg.go.dev/io)
  