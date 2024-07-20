---
title: "Rustのif let"
date: 2020-11-11T22:54:59+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/rust.png"
tags:
- Rust
---
  
Swift風に書いたら挙動が異なったのでメモ。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> rustc --version
rustc 1.44.1 (c7087fe00 2020-06-17)
```
  
Swift version 5.2.5  
  
## コード  
  
Swiftの感覚でRustのif letを書くと、予想に反する結果となるので注意。  
　
  
```rust
// rust
fn test(number: Option<u8>) {
    if let number = number {
        println!("{}", "hoge");  // hoge
    }
}

fn main() {
    let number = None;
    test(number);
}
```
  
hogeを出力してほしくなかった。  
  
　
  
```swift
// swift
func test(number: Optional<Int>) {
    if let number = number {
        print("hoge")  // 実行されない
    }
}

func main() {
    let number: Optional<Int> = nil
    test(number: number)
}

main()
```
  
ちなみにSwiftでif letを書くとこのようになる。  
  
　
  
```rust
// rust
fn test(number: Option<u8>) {
    if let Some(number) = number {
        println!("{}", "hoge");  // 実行されない
    }
}

fn main() {
    let number = None;
    test(number);
}
```
  
上記のSwiftと似たような結果を得るにはSomeで包む必要があった。  
Rustのif letでのOptionの代入時はデータの存在関わらずに代入されるので気をつける。  