---
title: "Int init(exactly:), init(clamping:), init(truncatingIfNeeded:) を試す"
date: 2021-09-10T21:04:11+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
使用したことがない Initializer があったので試す。
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 12.3
Build version 12C33
```
  
## init(exactly:)
  
> Creates an integer from the given floating-point value, if it can be represented exactly.  
> [init(exactly:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/int/3565927-init)  
  
「正確に表現できる場合は、指定された浮動小数点値から整数を作成する」  
  
　
  
```swift
let a = Int(exactly: 0.9)  // nil
let b = Int(exactly: 1.0)  // 1
let c = Int(exactly: 1.1)  // nil
```
  
小数点以下がすべて0の場合以外は nil を返す。  
  
## init(clamping:)
  
clamp: 固定する
  
> Creates a new instance with the representable value that’s closest to the given integer.  
> [init(clamping:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/int/2884879-init)  
  
「指定された整数に最も近い表現可能な値で新しいインスタンスを作成する」  
  
　
  
```swift
let a = Int8(clamping: 1111)  // 127
let b = Int8(clamping: -1111)  // -128

a == Int8.max  // true
b == Int8.min  // true
```
  
　
  
> Use the init(clamping:) initializer to create a new instance of a binary integer type where out-of-range values are clamped to the representable range of the type. For a type T, the resulting value is in the range T.min...T.max.  
> [swift/Integers.swift at main · apple/swift](https://github.com/apple/swift/blob/main/stdlib/public/core/Integers.swift#L488)  
  
ソースコード内のコメントのほうがより分かりやすく感じた。  
  
## init(truncatingIfNeeded:)
  
> Creates a new instance from the bit pattern of the given instance by truncating or sign-extending if needed to fit this type.  
> [init(truncatingIfNeeded:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/int/2926530-init)  
  
「このタイプに合わせる必要がある場合は、切り捨てまたは符号拡張することにより、指定されたインスタンスのビットパターンから新しいインスタンスを作成します。」
  
　
  
```swift
let a: UInt8 = 11  // 11 (00001011)
let b = UInt16(truncatingIfNeeded: a)  // 11 (00000000_00001011)


UInt16.max  // 65535 (11111111_11111111)
let c: Int = Int(UInt16.max) + 1  // 65535 (1_00000000_00000000)
let d = UInt16(truncatingIfNeeded: c)  // 0 (00000000_00000000)
```
  
> For example, when converting a 16-bit value to an 8-bit type, only the lower 8 bits of source are used.  
> [init(truncatingIfNeeded:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/int/2926530-init)  
  
「たとえば、16ビット値を8ビットタイプに変換する場合、ソースの下位8ビットのみが使用されます。」
  
あふれる時は下位ビットを優先する。 
  
## 参考  
  
- [init(exactly:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/int/3565927-init)  
- [init(clamping:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/int/2884879-init)  
- [init(truncatingIfNeeded:) | Apple Developer Documentation](https://developer.apple.com/documentation/swift/int/2926530-init)
  