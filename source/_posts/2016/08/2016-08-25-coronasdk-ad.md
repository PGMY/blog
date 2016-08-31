---
layout: post
title: "CoronaSDKで動画広告表示 AppLovin導入"
date: 2016-08-25 15:09:22 +0900
comments: true
category:
tags: []
keywords:
published: false
---

CoronaSDKを利用し始めて動画広告を導入する話がでてきたのでメモっておきます。  
当初CoronaSDKを使い始めた頃、[Corona Enterprise に頼らない簡単広告実装のススメ](http://qiita.com/fakestarbaby/items/5b43ef2cc8acb1f0d0de) を見て、WebViewにバナー広告を埋め込む形で実装してたんですが、「動画広告入れられないの？」という鶴の一声で動画広告の導入方法を探すことになりました。
上記記事は既に１年以上前の記事になっていて、どうやら現在では広告関連のプラグインが多数用意されているようです。  

参考：[Corona Docs Plugin #Ads / Monetization](https://docs.coronalabs.com/plugin/index.html#ads-monetization)  

この中から動画に対応しているものを探したのですが、最終的に [AppLovin](https://www.applovin.com/) を選びました。  
理由は動画の配信が安定していそうだからです。  

参考：  
[「AppLovin」 元Google 坂本達夫 氏が日本代表となった注目の広告ネットワーク/DSPについてまとめてみた 【@maskin】#AppLovin](http://techwave.jp/archives/what-is-about-applovin-com.html)  
[日本のモバイルアプリ広告に起きている３つのトレンド](https://www.appsflyer.com/jp/blog/three-trends-shaping-ad-buying-in-japan/)  

ふむ。ちょっと検索してみたらなんかよさ気な雰囲気。  


<!-- more -->
- [Getting Started with Corona SDK](https://coronalabs.com/resources/tutorials/getting-started-with-corona/)
- [Corona SDK API Matrix](http://developer.coronalabs.com/resources/apis/matrix)

- [CoronaSDKの有益なリソースはココにある！](http://qiita.com/fakestarbaby/items/49f00535c95ef9463eba)
- [広告webViewを実装した時に、意図せずブラウザが開いてしまう -Corona SDK-](http://www.createcreate.org/entry/2013/12/16/181730)
- [Luaで良く作る関数 ～table編～](http://lua.tips/index.php?Lua%20implements_func_table)
