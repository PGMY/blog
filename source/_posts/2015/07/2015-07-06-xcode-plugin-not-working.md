---
layout: post
title: "Xcodeをアップデートしたらプラグインが動かなくなったのを解決したメモ"
date: 2015-07-06 19:35:13 +0900
published: true
comments: true
category:
tags:
---

まー・・・・・毎回忘れる忘れる。  
特にBeta版のXcodeとか古いXcodeとか入れているとよくなるんですが、Xcodeをアップデートしたらプラグインが何度インストールしても使えなくなった！！ってことがよくあるわけです。  
とっても丁寧に解決方法を記載していただいている方がいらっしゃいましたありがとうございます＞△＜！！  

もはや管理しきれないブックマークに追加しても何の意味もなさない今日この頃（整理しろ・・・orz)  
ブログに書いておくのが手っ取り早いじゃーん！ってここに記載します。  

<!-- more -->

ありがとうございます先：  
[Xcode5.1〜6.2：Xcodeのバージョンをアップデートしたらプラグインが効かない、使えなくなった場合の対処方法](http://scrtree.github.io/blog/2015/03/11/resolved-xcode6-dot-2-update-problem/)  
ちなみに今回はXcode6.3から6.4にアップデートした時にまたかーとなりました。  


UUID自体を確認する

```
defaults read /Applications/Xcode.app/Contents/Info.plist DVTPlugInCompatibilityUUID
```

表示されたUUIDを追加する

```
find ~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins -name Info.plist | xargs -IFILE defaults write FILE DVTPlugInCompatibilityUUIDs -array-add 【UUIDをここに】
```

このあと再起動すれば読み込みできるようになります！
