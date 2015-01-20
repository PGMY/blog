---
layout: post
title: "Swift App Without Storyboard"
date: 2014-06-17 11:10:29 +0900
comments: true
category: iOS
tags: [Swift,]
keywords: Swift,iOS,Programming
published: true

---

Swiftのドキュメントを読みながらほむほむと言ってても身に付かないのでやっぱり何か作りつつ、動作確認しつつやっていかないとね！  
てことでSwiftでストーリーボードを使わずにアプリを作っていってみます・ω・  
（Swiftでアプリではなく、言語自体をさわってみたい場合はplaygroundも用意されています・ω・♪）

ストーリーボードを使わないのは言語の動きを確かめるためです(ストーリーボードをいつもあまり使ってないってのもありますが)

<div class="github-widget" data-repo="PGMY/FirstSwiftApp"></div>

<!-- more -->

# AppDelegateを読む

Emptyでプロジェクトを作成したら`AppDelegate.swift`が作成されるので内容をざっとみてみます。  

```
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: NSDictionary?) -> Bool {
        self.window = UIWindow(frame: UIScreen.mainScreen().bounds)
        // Override point for customization after application launch.
        self.window!.backgroundColor = UIColor.whiteColor()
        self.window!.makeKeyAndVisible()
        return true
    }

    -----
    略
    -----
}

```

わおΣ  
とりあえずmainはどこいったんだとかいろいろわからない。  

## main関数
main関数はなくなりましたΣ  
けどmain関数を利用するライブラリなどもあるわけで。使おうと思えば使えるみたいです。  
- [Swiftのプロジェクトでmain.mを使う](http://qiita.com/kaiinui@github/items/6307d8ebafbb56b3b0f8)

に記載されてましたΣ

## Objective-Cのコードを使うためのimport
swiftでObjective-Cのコードを使うには明示的にimportしてあげる必要があります。  
`UIApplicationDelegate`と`UIResponder`を利用したいので`AppDelegate`では`UIKit`をimportしてあげます。  

```
import UIKit
```

こうするとUIKitが使えるようになります。

## @UIApplicationMain
Objective-Cではmain関数でUIApplicationMainにAppDelegateを渡していましたが、Swiftでは`@UIApplicationMain`をAppDelegateクラスのすぐ上に記述することで、同様の内容になるようです。  
ということで、今まであったmain関数の中の処理の代わりに、`@UIApplicationMain`をAppDelegateクラスのswiftファイルに記述します。  

## クラス
クラスの書き方は

```
class [クラス名] : [親クラス,デリゲート...] {
}
```
こんな形になるみたいです。  
`:`のすぐ後ろの継承したいクラス名、続けてプロトコル名を記載していきます。  
もちろんクラスは複数継承することはできないので、親クラスは１つしか記載することができません。  
複数のクラスを記述した時は

```
 Multiple inheritance from classes 'UIXXXXX' and 'UIXXXXXX'
```
こんな感じで怒られましたorz

## プロパティ宣言
プロパティの宣言はクラスの一番上に記載します。  

```
var window: UIWindow?
```

`UIWindow?`の最後にある`?`は`nil`を許容するかどうかです。  
初期値は`nil`なので、宣言時に初期値を代入しない場合、`?`をつけておかないとエラーになってしまいます。  
これとは別に、`nil`を入れたくない変数の場合は`!`を記載しておくと、変数に`nil`を代入しようとするとエラーになるようになっています。  
※このエラーが起きるのは実行時です。

## 関数

```
func [関数名]([引数変数]:[引数型], ...) -> [戻り値型]{
}
```
こんな感じで記載します。  

Objective-C

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
```
Swift

```
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: NSDictionary?) -> Bool
```

## Windowの初期化処理

記載する内容は同じですが、ここもSwiftの記述に書き換えます。  

Objective-C

```
self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
```

Swift

```
self.window = UIWindow(frame: UIScreen.mainScreen().bounds)
```

Swiftだと初期化時に`alloc`をわざわざ呼び出す必要がなくなりました。  
今までのものをSwiftに書き換える場合、初期化を表す`init`と`initWith`は切り取り、その後ろの続く部分の先頭を小文字にして`()`の一番前の部分に記載します。  
`initWithFrame:` => `(frame: )`

Objective-C

```
UITableView *myTableView = [[UITableView alloc] initWithFrame:CGRectZero style:UITableViewStyleGrouped];
```

Swift

```
let myTableView: UITableView = UITableView(frame: CGRectZero, style: .Grouped)
```

ほかのものも同様にこのような形に変換されています。　　
ちなみに`.Grouped`の`.`はenumであることを表しています。

```
self.window!.backgroundColor = UIColor.whiteColor()
```

この`self.window!`にある`!`はやはり`nil`を許容しない意味で、`self.window.backgroundColor`に値を代入する際、`self.window`が`nil`であってはいけないため`!`をつけておきます。  

# ViewControllerをセットする

今回はよくある形のNavigationControllerに独自のViewControllerを持たせる形にしてみます。

## SwiftViewController

```
import UIKit

class SwiftViewController: UIViewController {
    init(nibName nibNameOrNil: String!, bundle nibBundleOrNil: NSBundle!) {
        super.init(nibName:nibNameOrNil, bundle:nibBundleOrNil)
        self.view!.backgroundColor = UIColor.redColor()
    }
    override func viewDidLoad(){
        super.viewDidLoad()
    }
}
```

ViewControllerはいつも通り初期化メソッドと`viewDidLoad`を作っておきます。
わかりやすいように背景色を赤にしてみました。

## AppDelegateで初期化

```
var navigationController: UINavigationController?
```

`navigationController`をプロパティに持たせて、`SwiftViewController`と一緒に初期化します。

```
var swiftController: SwiftViewController? = SwiftViewController(nibName: nil, bundle: nil)
self.navigationController = UINavigationController(rootViewController: swiftController)
self.window!.rootViewController = self.navigationController
```

これで第一段階完成！  
無事コントローラーを１つセットして、基本となる形を作り出すことができました。



# 参考サイト
- [Create application without storyboard in Swift language – iOS8](http://suslovjb.wordpress.com/2014/06/09/create-application-without-storyboard-in-swift-language-ios8/)
- [How do I create a new Swift project without using Storyboards?](http://stackoverflow.com/questions/24046898/how-do-i-create-a-new-swift-project-without-using-storyboards)
- [[Swift] Swiftのoptional valueの便利さ /「?」と「!」でより堅牢なコードへ](http://qiita.com/nori0620/items/afa94cc42f3233f54bdc)
- [Swiftのプロジェクトでmain.mを使う](http://qiita.com/kaiinui@github/items/6307d8ebafbb56b3b0f8)
