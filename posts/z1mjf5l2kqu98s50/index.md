---
title: "Rustのタプルの所有権"
date: 2020-10-31T21:27:07+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/rust.png"
tags:
- rust
---
  
Rustの所有権、慣れるまで時間がかかりそう。  
<!--more-->  
  
## 開発環境  
  
```bash
> rustc --version
rustc 1.44.1 (c7087fe00 2020-06-17)
```
  
## タプル  
異なる型の値の集合。  
所有権周りを理解していく。  
  
## 例1  
  
```rust
fn main() {
    let t1 = ("a", "b");
    let t2 = t1;
    println!("{}", t1.0);  // a
}
```
  
`t1`はタプルに束縛され、所有権を保持。  
`t2`は`t1`のコピーに束縛され、所有権を保持。  
`t1`は所有権を保持しているため、`println!`で値を出力できる。  
  
なぜ所有権が移動せずに、コピーが行われ保持されるのか。  
  
> If every type inside a tuple implements one of the following traits, then a tuple itself also implements it.  
> - ...  
> - Copy  
> (from [tuple - Rust](https://doc.rust-lang.org/std/primitive.tuple.html#trait-implementations-1))  
  
タプル内の値の型が全てCopyトレイトを実装している場合、タプル自体もそれが適用される。  
  
## 例2  
タプルに自作構造体を入れた。  
  
```rust
// コンパイルエラー
fn main() {
    struct Point {
        x: i32,
        y: i32,
    }

    // println!に流すためのトレイト
    impl std::fmt::Display for Point {
        fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::result::Result<(), std::fmt::Error> {
            write!(f, "({}, {})", self.x, self.y)
        }
    }

    let u1 = (Point{x: 0, y: 1}, "b");
    let u2 = u1;
    println!("{}", u1.0);  // error[E0382]: borrow of moved value: `u1`
}
```
  
`u1`はタプルに束縛され、所有権を保持。  
`u2`は`u1`のタプルに束縛され、u1から所有権を移動。  
`u1`は所有権を保持していないため、`println!`で値を出力できない。  
  
自作構造体`Point`にはCopyトレイトが実装されていないため、大元のタプルもCopyトレイトが適用されない。  
  
## 例3  
`Point`にCopyトレイト, Cloneトレイトを実装した。  
  
Copyトレイトを実装するにはCloneトレイトを実装しなければならない。  
Swiftでいえば、プロトコルが二層(直列)になっているイメージ。  
  
```rust
fn main() {
    struct Point {
        x: i32,
        y: i32,
    }

    impl std::fmt::Display for Point {
        fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::result::Result<(), std::fmt::Error> {
            write!(f, "({}, {})", self.x, self.y)
        }
    }

    impl Copy for Point { }

    impl Clone for Point {
        fn clone(&self) -> Self {
            *self  // `*`は参照されている値を得るため
        }
    }

    let v1 = (Point{x: 0, y: 1}, "b");
    let v2 = v1;
    println!("{}", v1.0);  // (0, 1)
}
```
  
`v1`はタプルに束縛され、所有権を保持。  
`v2`は`v1`のコピーに束縛され、所有権を保持。  
`v1`は所有権を保持しているため、`println!`で値を出力できる。  
  
　
  
---
  
```rust
impl Clone for Point {
    fn clone(&self) -> Self {
        *self  // `*`は参照されている値を得るため
    }
}
```
  
コメントでも書いたが、`*`(アスタリスク)は参照自体ではなく、参照されている値を得るために使う。  
  
```rust
// コンパイルエラー
fn main() {
    let x = 7;
    let y = &x;
    assert_eq!(7, y);  // error[E0277]: can't compare `{integer}` with `&{integer}`
}
```
  
　
  
```rust
fn main() {
    let x = 7;
    let y = &x;
    assert_eq!(7, *y);  // assertが発生しない → 値の比較ができており同値である
}
```
  
## 参考  
- [tuple - Rust](https://doc.rust-lang.org/std/primitive.tuple.html)  
- [std::marker::Copy - Rust](https://doc.rust-lang.org/std/marker/trait.Copy.html)  
- [std::clone::Clone - Rust](https://doc.rust-lang.org/std/clone/trait.Clone.html)