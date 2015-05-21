---
layout: post
title: "Swiftメモ"
date: 2014-06-18 16:29:16 +0900
comments: true
category: iOS
tags: [Swift]
keywords: Swift,Memo
published: true

---

記事になる前のとにかくSwiftについてメモしていく場所

<!-- more -->

```
extension
```
クラスを拡張できる。
⇒[Control Structure with Extension](http://qiita.com/Lewuathe/items/fb7928d846e466031ae0)


```
mutating
```
いわゆる`const`？
⇒[Swift and mutating struct](http://stackoverflow.com/questions/24035648/swift-and-mutating-struct)


```
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = three + pointOneFourOneFiveNine
```

⇒エラーになる。　`Double(three)`ってしてあげないと　Integer + Doubleの足し算が用意されていない・・・？


```
typealias
```
defineみたいに使えるやつかな？


Swiftの`nil`はObjective-Cの`nil`とは意味がちがう。
Objective-Cは`nil`はポインタ
