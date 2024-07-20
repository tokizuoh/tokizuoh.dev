---
title: "Rustでランダム文字列を生成するCLIを作った"
date: 2020-11-03T15:06:08+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/rust.png"
tags:
- rust
---
  
[Swiftで書いたコード](https://tokizuoh.dev/posts/jo4d9a0mm7hs975d/)をRustで書き直した。  
<!--more-->  
[Hugoの記事タイトルを無心で決める](http://localhost:1313/posts/dyiy0cm1zzmkb996/)にて使用。  
  
## 開発環境  
  
```bash
> rustc --version
rustc 1.44.1 (c7087fe00 2020-06-17)
```
  
## コード  
  
GitHub: [tokizuoh/lechenaultia](https://github.com/tokizuoh/lechenaultia)  
  
```rust
use structopt::StructOpt;
use rand::{thread_rng, Rng};

#[derive(StructOpt)]
struct Argument {
    number_of_digits: u64,
}

fn main() {
    let args = Argument::from_args();

    let mut alphanumerics = (0..26).map(|x| (x + b'a') as char)
        .collect::<Vec<_>>();
    let mut numbers = (0..10).map(|x| (x + b'0') as char)
        .collect::<Vec<_>>();
    alphanumerics.append(&mut numbers);
    
    let mut result = String::new();
    let mut rng = thread_rng();
    for i in 0..args.number_of_digits {
        let upper_bound = if i == 0 { 10 } else {36};
        result = result + &alphanumerics[rng.gen_range(0, upper_bound)].to_string();
    }

    println!("{}", result);
}
```
  
1文字目がアルファベットになるようにコードを書いたが、これがなければmapで処理できる。  
  
## 実行
  
```bash
> cargo run --release 10
    Finished release [optimized] target(s) in 0.02s
     Running `target/release/lechenaultia 10`
ho4v3g3tk4
```
  
　
  
```bash
> cargo run --release
    Finished release [optimized] target(s) in 0.02s
     Running `target/release/lechenaultia`
error: The following required arguments were not provided:
    <number-of-digits>

USAGE:
    lechenaultia <number-of-digits>

For more information try --help
```
  
引数の欠落や誤った値を渡して実行すると詳しいエラーを出力。  
  
## 参考  
- [Getting started - Command Line Applications in Rust](https://rust-cli.github.io/book/index.html)  
- [structopt - Rust](https://docs.rs/structopt/0.3.20/structopt/)  