---
layout: post
title: "gcc-warning-options"
date: 2015-09-08 10:59:18 +0900
comments: true
category: iOS
tags: [Xcode,Warning,]
keywords:
published: true
---

gccの警告オプションを弄ぶ機会があったので調べてたら・・・  
きっちりしっかりまとまっているところがありました本当にありがとうございます！！！  

ということでただのブックマークメモですΣ＞＜  

## 日本語
[gcc の警告オプションを調べてみた。](https://gist.github.com/tosihisa/2585404)

## 英語
[3.8 Options to Request or Suppress Warnings](https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html)

いやぁ・・・まとめて載ってるって便利！

# 警告の無視
警告が無視したくて探したのでこれもついでに。
ignoredの後ろのオプションに上記の無視したい対象オプションを指定すればオッケーです。

```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wsign-compare"
#pragma clang diagnostic pop
```

ーωー*
