---
layout: post
title: "背景が時計のHexColorになる時計"
date: 2014-06-24 14:21:12 +0900
comments: true
category: iOS
tags: ["Hex", "Swift"]
keywords: Hex, Color, Watch
published: true

---
[HexClock](http://www.jacopocolo.com/hexclock/)というサイトを見つけました・ω・  
どうやら背景が時計の時間・分・秒で構成された６桁の数字の色になるサイト。

面白い＞ω＜  
ので、アプリにしてみた。  
<div class="github-widget" data-repo="PGMY/HexColorWatch"></div>

さて、これをせっかくなのでSwiftにするべく奮闘していこうと思いまする。  
<!-- more -->
# Objective-CのコードをSwiftへ切り替えていく
## カテゴリ作成
UIColorを拡張してHexのStringからUIColorを生成する関数を追加する形をとってます。  
Swiftでのカテゴリは`extension`でいいのかな？  
ファイル名は`UIColor+Hex.swift`でいいのかな？  
とりあえず`UIColor+Hex.swift`にしてみます。  

```
extension UIColor {
  class func color(hex: Int) -> UIColor{
    let red :CGFloat = CGFloat(((hex & 0xff000000) >> 24))/225.0
    let green :CGFloat = CGFloat(((hex & 0x00ff0000) >> 16))/225.0
    let blue :CGFloat = CGFloat(((hex & 0x0000ff00) >> 8))/225.0
    let alpha :CGFloat = CGFloat((hex & 0x000000ff))/225.0
    return UIColor(red: red, green: green, blue: blue, alpha: alpha)
  }
  class func color(hexString:String) -> UIColor {
    var hex:UInt32 = 0x0;
    var scanner:NSScanner = NSScanner(string: hexString)
    scanner.scanHexInt(&hex)
    return self.color(Int(hex))
  }
}
```

こんな感じかな？  
関数名の付け方がまだいまいちですorz  
とりあえずこれで行きます・ω・´  

## NSTimerをセットする
しまったセレクタの書き方がわからないΣ  
Objective-Cでいう`@selector(hoge:)`の部分。  
Timerを使って

```
self.timer = [NSTimer scheduledTimerWithTimeInterval:0.01
                                              target:self
                                            selector:@selector(onTimer:)
                                            userInfo:nil
                                             repeats:YES];
```

と書いていたところをSwiftにするには・・・。

```
self.timer = NSTimer.scheduledTimerWithTimeInterval(0.01,target:self, selector:"onTimer", userInfo:nil, repeats:true)
```

```
func onTimer(){
  println("Timer")
}
```

こんな形になりました。  
引数つきのセレクタってどうするんだろう・・・Σ  
とりあえずこれでいく！！

と思ったら記事にしていただいている方たいらっしゃいました。  
⇒[SwiftでiOS SDKの「複数の引数がある@selector」を使う方法。](http://tototti.hatenablog.com/entry/2014/06/19/205820)  
普通に`:`つけるだけでした

```
self.timer = NSTimer.scheduledTimerWithTimeInterval(0.01,target:self, selector:"onTimer:", userInfo:nil, repeats:true)
```

```
func onTimer(timer:NSTimer){
  println("Timer")
}
```

そりゃそうだ。ちょっと寝ぼけてた・ω・｀

# 問題がおきた
さて。  
<div class="github-widget" data-repo="PGMY/HexColorWatch"></div>
ここに一応Objective-CのソースとSwiftのソース(プロジェクトファイルからはSwiftを参照するようになってまする)を置いてあるわけですが・・・  
Objective-Cのソースをそのまんまの形でSwiftにしたんだけど(不要なViewつくってたのでそれはなくした)画面がチカチカ点滅するようになってしまいましたorz  

## 原因
onTimerの中の処理を

```
var df : NSDateFormatter = NSDateFormatter()
df.dateFormat = "HHmmss"
var str:String = df.stringFromDate(NSDate.date())
self.backGroundView!.backgroundColor = UIColor.color(str+"FF")
self.hexWatchLabel!.text = "#"+str
```

こんな形にしていました。  
が、呼び出したかった`class func color(hexString:String) -> UIColor`ではなく、`class func color(hex: Int) -> UIColor`に入ってきてたのが原因でした。  
ほむ・・・  
片方の引数を`hexString`とかしちゃってたのがだめなのかと思って統一してみても駄目でした。  

```
class func color(hex: Int) -> UIColor
class func color(hex:String) -> UIColor
```

playgroundで同じ状況を作ってみる。


```
class SampleClass {
  class func testFunc(a:String){
    println("String")
  }

  class func testFunc(a:Int){
    println("Int")
  }
}

SampleClass.testFunc("TEST")
SampleClass.testFunc(123)
```

出力結果は

```
String
Int
```

うぅむ・・・わからないΣ

## 解決
Optional Valueをつけてないのが原因だったみたい？です。

```
class func color(hex: Int!) -> UIColor
class func color(hex:String!) -> UIColor
```

引数のところに`!`をつけるといけましたΣ  
うぅむ・・・！！！！  
まだ慣れない。  




ということでここで一旦完成です。



# お世話になりました
- [Swiftの文字列操作まとめ](http://nsblogger.hatenablog.com/entry/2014/06/09/swift_string)
- [[Swift] ちょっとしたメモ](http://qiita.com/edo_m18/items/ce4e436b78db2e56542d)
- [SwiftでiOS SDKの「複数の引数がある@selector」を使う方法。](http://tototti.hatenablog.com/entry/2014/06/19/205820)
