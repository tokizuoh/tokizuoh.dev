---
title: "Stringに対するmatch式, 文字列の連結(1)"
date: 2020-11-09T21:08:55+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/rust.png"
tags:
- rust
---
  
vs 文字列
<!--more-->  
  
## 開発環境  
  
```bash
> rustc --version
rustc 1.44.1 (c7087fe00 2020-06-17)
```
  
## コード  
下記のように書きたかったが、エラーが出たので対処する。  
  
```rust
fn main() {
    let a = "word".to_string();
    let b = match a {
        "word" => a + "-suffix",  // mismatched types: expected struct `std::string::String`, found `&str`
        _ => "prefix-" + a,  
    };
    println!("{}", b);
}
```
  
> expected struct `std::string::String`, found `&str`
  
`a` がStringであるのに対し、文字リテラル（&str）を比較すな、と言われます。  
パターン(`"word"`)をStringにしてみる。  
  
　
  
```rust
fn main() {
    let a = "word".to_string();
    let b = match a {
        "word".to_string() => a + "-suffix",  // error: expected one of `...`, `..=`, `..`, `=>`, `if`, or `|`, found `.`
        _ => "prefix-" + a,
    };
    println!("{}", b);
}
```
  
> expected one of `...`, `..=`, `..`, `=>`, `if`, or `|`, found `.`
  
パターンの後に続いて記述できるのは上記のみ。（パターンに使うrangeなど）  
  
方向転換してパターンを変えるのではなく、matchさせる値を&strにしてみる。  
  
　
  
```rust
fn main() {
    let a = "word".to_string();
    let b = match &*a {
        "word" => a + "-suffix",
        _ => "prefix-" + a,  // error: `+` cannot be used to concatenate a `&str` with a `String`
    };
    println!("{}", b);
}
```
  
Stringに*を付与するとstrになり、strに&を付与すると&strになる。  
先程のエラーは解消されたが、もう一つエラーが残っている。  
エラー文だけでは分からなかったのでError Indexで調べると下記の記述があった。  
  
> String concatenation appends the string on the right to the string on the left and may require reallocation. This requires ownership of the string on the left. If something should be added to a string literal, move the literal to the heap by allocating it with to_owned() like in "Your text".to_owned().  
> (from [Rust Compiler Error Index](https://doc.rust-lang.org/error-index.html#E0369))
  
文字列リテラルに何か追加する際は、文字列リテラルに所有権が必要とのこと。  
  
　
  
```rust
fn main() {
    let a = "word".to_string();
    let b = match &*a {
        "word" => a + "-suffix",
        _ => "prefix-".to_owned() + a,  // error: expected `&str`, found struct `std::string::String`
    println!("{}", b);
}
```
  
新たなエラー。  
  
>  error: expected `&str`, found struct `std::string::String`
  
型を合わせる。  
  
　
  
```rust
fn main() {
    let a = "word".to_string();
    let b = match &*a {
        "word" => a + "-suffix",
        _ => "prefix-".to_owned() + &*a,  // &a でも通る（なんで？？）
    };
    println!("{}", b);  // word-suffix
}
```
  
無事出力できたが、文字列の連結について謎があるので次回に行う。  
  
## 参考  
- [Rust Compiler Error Index](https://doc.rust-lang.org/error-index.html#E0369)  
- [std::borrow::ToOwned - Rust](https://doc.rust-lang.org/std/borrow/trait.ToOwned.html)  
