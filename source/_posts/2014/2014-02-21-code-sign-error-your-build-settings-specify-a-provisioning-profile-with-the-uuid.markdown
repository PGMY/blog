---
layout: post
title: "Code Sign error: Your build settings specify a provisioning profile with the UUID"
date: 2014-02-21 15:53:08 +0900
comments: true
category: iOS
tags: [Error,CodeSign,Xcode]
---


```
Check dependencies
Code Sign error: No matching provisioning profile found: Your build settings specify a provisioning profile with the UUID XXXXX-XXXXX-XXXXX, however, no such provisioning profile was found.
CodeSign error: code signing is required for product type 'Application' in SDK 'iOS 7.0'

** BUILD FAILED **
```

上記エラーが出た時。

project.pbxprojをメモ帳とかで開いてPROVISIONING_PROFILEがある行を全て削除すればいいみたい。