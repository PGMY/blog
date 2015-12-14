---
layout: post
title: "UIViewから動画を生成するにあたってのいろいろ"
date: 2015-11-25 13:18:05 +0900
comments: true
category:
tags: []
keywords:
published: false
---


## アニメーションView録画ライブラリGlimpse
<div class="github-widget" data-repo="wess/Glimpse"></div>
こちらを大変参考にさせてもらいました。  
が、更新が２年前とのこともあってか、いろいろと不具合もあったり、いくつかわからない部分もあったので、結局いくつか手を加えました。  
※時間があれば、自分で作ってみようかなーなんて。  

### 録画時の表示がどうしても重くなる
動作としては、UIViewのキャプチャを撮影して、連番画像を生成し、そこから動画を生成しています。  
欲しかった機能としては、バックグラウンドで表示させているViewをそのまま動画に変換したかったのですが、どうしても動作が重い。  
原因は表示はさせていないものの、キャプチャ時にメインスレッドでアニメーションをさせる必要があるからのようでした。    


[UIGraphicsBeginImageContextWithOptions and Multithreading](http://stackoverflow.com/questions/10931155/uigraphicsbeginimagecontextwithoptions-and-multithreading)  

> So, after a couple of days of investigating, how come manipulating the UIKit context is thread-safe and yet you don't seem to be able to create one in a thread other than the main because UIGraphicsBeginImageContextWithOptions "should be called only on the main thread", but still doing so works perfectly fine and after reading some small posts on the subject and discussing it with other guys on Apple's developer forums I can clearly state that, what's said in the documentation about UIGraphicsBeginImageContextWithOptions, UIGraphicsPushContext and UIGraphicsPopContext is WRONG and those methods could be called and the context used in another thread without problems. So UIGraphicsBeginImageContextWithOptions, UIGraphicsPushContext and UIGraphicsPopContext are thread-safe.


なるほど。英語は苦手orz  


### 画像から動画を生成する
[(iOS) 静止画から動画を生成する](http://chicketen.blog.jp/archives/8020857.html)  
[iOS Tips #7 画像から動画を作成する | アドカレ2013 : SP #15](http://dev.classmethod.jp/smartphone/iphone/ios-tips-7-movie-from-images/)  
[iOS7対応高速(15倍速い)スクリーンキャプチャ](http://qiita.com/nasu_st/items/561d8946966015abd448)  
[save many UIImage to NSMutableArray without memory leak or similar method](http://stackoverflow.com/questions/19034555/save-many-uiimage-to-nsmutablearray-without-memory-leak-or-similar-method)

### CMTimeの使い方
[(Swift)CMTimeを使う](http://nackpan.net/blog/2015/10/15/swift-cmtime/)
[iPhoneアプリ開発: UNIX時間、エポックからのミリ秒の取得](http://ishidak.blogspot.jp/2010/05/iphone-unix.html)

## ということで、バグを幾つか修正したものがこちらにあります。
最初試しに触って見てただけなのでブランチ名はpgmyになってます。  
pgmyブランチに修正版が入っています！！！！  
ご注意ください。  

<div class="github-widget" data-repo="PGMY/Glimpse"></div>
