---
title: "Rustの文字列（&str, String）"
date: 2020-11-01T12:14:29+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/rust.png"
tags:
- rust
---
  
どのタイミングでどっちを使うのか分からなかったのでメモ。  
<!--more-->  
  
## 開発環境  
  
```bash
> rustc --version
rustc 1.44.1 (c7087fe00 2020-06-17)
```
  
## 所有権  
  
#### &str  
文字列リテラルは&str。
  
```rust
fn main() {
    let s = "Hello";
    let t = s;
    println!("{}", s);  // Hello
}
```
  
`s`は &str に束縛され、所有権を保持。  
`t`は`s`の文字列のコピーに束縛され、所有権を保持。  
`s`は所有権を保持しているため、`println!`で値を出力できる。  
  
&str にはCopyトレイトが実装されているため、コピーが可能。  
　
  
#### String
  
```rust
// コンパイルエラー
fn main() {
    let s = "Hello".to_string();  // &str -> String
    let t = s;
    println!("{}", s);  // error[E0382]: borrow of moved value: `s`
}
```
  
`s`は String に束縛され、所有権を保持。  
`t`は`s`の文字列に束縛され、所有権が`s`から`t`に移動。  
`s`は所有権を保持していないため、`println!`で値を出力できない。  
  
String にはCopyトレイトが実装されていないため、コピーができない。  
  
ちなみに、`to_string()`は`to_owned()`でもStringに変換可能。  
  
## 借用と参照  
  
#### &str
[参照と借用](https://doc.rust-jp.rs/book-ja/ch04-02-references-and-borrowing.html)のコードのStringを&strに変更。  
  
```rust
fn main() {
    let s1 = "hello";

    let len = calculate_length(s1);

    // '{}'の長さは、{}です
    println!("The length of '{}' is {}.", s1, len);  // The length of 'hello' is 5.
}

fn calculate_length(s: &str) -> usize {
    s.len()
}
```
  
　
  
```rust
let len = calculate_length(s1);
```
  
値をそのまま実引数として渡していて、所有権移動しないのか？と思ったが、  
  
> its(&str) contents are borrowed.  
> (from [std::str - Rust](https://doc.rust-lang.org/std/str/index.html))  
  
より、所有権の移動は発生しておらず、**借用**が行われている。  
借用とは、関数の引数に参照を取ることを指す。  
文字列リテラル自体が既に &str なので、引数に渡した時点で借用が発生する。  
  
#### String
[参照と借用](https://doc.rust-jp.rs/book-ja/ch04-02-references-and-borrowing.html)のコードを見る。  
  
```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    // '{}'の長さは、{}です
    println!("The length of '{}' is {}.", s1, len);  // The length of 'hello' is 5.
}

fn calculate_length(s: &String) -> usize {
    s.len()
}

```
  
もし、`calculate_length`に`s1`のみを渡すと、コンパイルエラーになる。  
  
```rust
// コンパイルエラー
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(s1);  // ここで関数にs1の所有権を移動させている

    // '{}'の長さは、{}です
    println!("The length of '{}' is {}.", s1, len);  // error[E0382]: borrow of moved value: `s1`
}

fn calculate_length(s: String) -> usize {
    s.len()
}
```
  
## 使い分け  
値の追加などしない限りは &str で良いんじゃない？という感想に落ち着いたが、使っていくうちに何か分かるだろう。  
  
## 参考  
- [std::str - Rust](https://doc.rust-lang.org/std/str/index.html)  
- [std::string::String - Rust](https://doc.rust-lang.org/std/string/struct.String.html)  
- [参照と借用 - The Rust Programming Language 日本語版](https://doc.rust-jp.rs/book-ja/ch04-02-references-and-borrowing.html)  
