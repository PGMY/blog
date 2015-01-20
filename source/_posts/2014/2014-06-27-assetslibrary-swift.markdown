---
layout: post
title: "Assetsを表示するプログラムをSwiftにしてみる"
date: 2014-06-27 14:35:19 +0900
comments: true
category: iOS
tags: ["Swift","AssetsLibrary"]
keywords: AssetsLibrary, Swift
published: true

---

カメラロールの写真をAssetsLibraryを使って取り出して日付順にソートするプログラムがありまする。

<div class="github-widget" data-repo="PGMY/AssetsLibrary"></div>

これをSwiftに書き換えていく記録をつけていきますー。  
作業を順番に記録していくので構成はちょっとバラバラするかもしれません・・・。

*つっこみ募集中*

<!-- more -->

# 基本部分の書き換え

## main関数とAppDelegate
Swiftに書き換えるにはmain関数のファイルを削除し、AppDelegateクラスに`@UIApplicationMain`をつければオッケーです。  

```
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

  var window: UIWindow?
  var myRootViewController: MYRootViewController?

  func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: NSDictionary?) -> Bool {
    self.window = UIWindow(frame: UIScreen.mainScreen().bounds)
    self.window!.backgroundColor = UIColor.whiteColor()
    self.myRootViewController = MYRootViewController(nibName: nil, bundle: nil)
    self.window!.rootViewController = self.myRootViewController;
    self.window!.makeKeyAndVisible()
    return true
  }
  .....
```

こんな感じ。もはやいつもの光景

## TabBarControllerでぷち詰まり

```
self.dateViewController = DateViewController(nibName:nil, bundle:nil)

var tabs = [self.dateViewController!]
let tabBarController = UITabBarController()
tabBarController.setViewControllers(tabs, animated: true)
```

ここ。

```
var tabs = [self.dateViewController]
```

`!`を書かなかったばっかりに`.setViewControllers`でエラーが出てしまって何が原因かわからずちょっと詰まってしまいました。  
まだ慣れないorz

## enumで宣言されているAssets Group Typeの指定

Objective-C

```
ALAssetsGroupType assetsGroupType = ALAssetsGroupAlbum | ALAssetsGroupEvent | ALAssetsGroupFaces | ALAssetsGroupSavedPhotos;
```

これをSwiftにしようと思ったのですが、Objective-Cでは

```
enum {
    ALAssetsGroupLibrary        = (1 << 0),         // The Library group that includes all assets.
    ALAssetsGroupAlbum          = (1 << 1),         // All the albums synced from iTunes or created on the device.
    ALAssetsGroupEvent          = (1 << 2),         // All the events synced from iTunes.
    ALAssetsGroupFaces          = (1 << 3),         // All the faces albums synced from iTunes.
    ALAssetsGroupSavedPhotos    = (1 << 4),         // The Saved Photos album.
#if __IPHONE_5_0 <= __IPHONE_OS_VERSION_MAX_ALLOWED
    ALAssetsGroupPhotoStream    = (1 << 5),         // The PhotoStream album.
#endif
    ALAssetsGroupAll            = 0xFFFFFFFF,       // The same as ORing together all the available group types,
};
typedef NSUInteger ALAssetsGroupType;
```

こんな風に宣言されてましたが、Swiftでは

```
var ALAssetsGroupLibrary: CUnsignedInt { get } // The Library group that includes all assets.
var ALAssetsGroupAlbum: CUnsignedInt { get } // All the albums synced from iTunes or created on the device.
var ALAssetsGroupEvent: CUnsignedInt { get } // All the events synced from iTunes.
var ALAssetsGroupFaces: CUnsignedInt { get } // All the faces albums synced from iTunes.
var ALAssetsGroupSavedPhotos: CUnsignedInt { get } // The Saved Photos album.

var ALAssetsGroupPhotoStream: CUnsignedInt { get } // The PhotoStream album.

var ALAssetsGroupAll: CUnsignedInt { get } // The same as ORing together all the available group types,

typealias ALAssetsGroupType = Int
```

こう変わっています。  
ということで、普通に

```
let assetsGroupType :ALAssetsGroupType = ALAssetsGroupAlbum | ALAssetsGroupEvent | ALAssetsGroupFaces | ALAssetsGroupSavedPhotos;
```

こう書いてしまうと、`ALAssetsGroupType`はInt型で宣言されてるため、エラーがでてしまいます。  
ということでそのまま型変換して解決させてみました。

```
let assetsGroupType :ALAssetsGroupType = Int(ALAssetsGroupAlbum) | Int(ALAssetsGroupEvent) | Int(ALAssetsGroupFaces) | Int(ALAssetsGroupSavedPhotos);
```

<s>⇒　これじゃ駄目？でした。そもそも`import AssetsLibrary`と書いているにもかかわらず

```
error: use of unresolved identifier 'ALAssetsGroupAll'
```
が出てしまいます。  
⇒　絶賛調査中</s>  

Xcodeがアップデートされて読み込めるようになりました・ω・  
最終的な形はこうなりました。

```
let assetsGroupType :ALAssetsGroupType!  = ALAssetsGroupType(ALAssetsGroupAlbum|ALAssetsGroupEvent|ALAssetsGroupFaces|ALAssetsGroupSavedPhotos)
```


## Blocksをclosureへ

AssetsLibraryのAssetsごとに入ってくるBlocksをSwiftのClosureに書き換えます。

```
ALAssetsGroupEnumerationResultsBlock groupResultBlock = ^(ALAsset *asset, NSUInteger index, BOOL *stop) {
  if (asset) {
    NSDate   *assetDate    = [asset valueForProperty:ALAssetPropertyDate];
    NSString *assetDateStr = [formatter stringFromDate:assetDate];
    [allAssets addObject:@{ ASSET: asset, DATE: assetDate, DATE_STR: assetDateStr }];
  }
};
```

`allAssets`は日付ソートをするための`Array`です。  
これをClosureにすると

```
var groupResultBlock : ALAssetsGroupEnumerationResultsBlock = { (asset: ALAsset!, index: Int, stop:CMutablePointer<ObjCBool>) -> Void in
  if asset {
    let assetDate: NSDate = asset.valueForProperty(ALAssetPropertyDate) as NSDate
    let assetDateStr = fomatter.stringFromDate(assetDate)
    allAssets.append([self.ASSET:asset, self.DATE:assetDate, self.DATE_STR:assetDateStr])
  }
}
```

こんな感じになりました！  
クロージャーは`{(引数) -> 戻り値 in }`といった書き方になるみたいです。  
(正直このクロージャーがイマイチまだ理解しきれてないので学びながら進みたいと思います。)

## 日付で配列をソートする

Objective-Cではソートをするために

```
NSComparator comparetor = ^NSComparisonResult (id obj1, id obj2) {
  NSDate *datea = obj1[DATE];
  NSDate *dateb = obj2[DATE];

  return [dateb compare:datea];
};
```
こんな感じで`NSComparator`を使って日付ソートしていました。  
これをSwiftで同じ形に書き換えてもいいんですが、SwiftにはArrayの`sort`が存在するのでそれ使ってみます。  

-----

問題発生中につきここから先はメモになりますorz  

めも  
Expression resolves to an unused l-value

fatal error: Can't unwrap Optional.None

## SwiftでSingleton



[絶賛更新中]


# 参考サイト

- [How to declare a block with arguments in swift?](http://stackoverflow.com/questions/24026497/how-to-declare-a-block-with-arguments-in-swift)
- [How to use Objective-C code with #define macros in Swift](http://stackoverflow.com/questions/24133695/how-to-use-objective-c-code-with-define-macros-in-swift)
- [Don't understand why swift wants me to be explicit](http://stackoverflow.com/questions/24124635/dont-understand-why-swift-wants-me-to-be-explicit)
- [Swift でシングルトン](http://qiita.com/1024jp/items/3a7bc437af3e79f74505)
- [hpique/SwiftSingleton](https://github.com/hpique/SwiftSingleton)
- [dispatch_once singleton model in swift](http://stackoverflow.com/questions/24024549/dispatch-once-singleton-model-in-swift)
- [Swift first steps: Singleton](http://synappse.co/swift-first-steps-singleton/)
- [The Swift Programming Language - Properties（プロパティ）をまとめる](http://qiita.com/kiyotaman/items/54fe453498513fe92164)
- [SINGLETON PATTERN IN SWIFT](http://vperi.com/2014/06/05/singleton-pattern-in-swift/)
- [Swift alternative to performSelectorOnMainThread](http://stackoverflow.com/questions/24126261/swift-alternative-to-performselectoronmainthread)
- [How do I create a UIImage from a CGImage in Swift?](http://stackoverflow.com/questions/24319586/how-do-i-create-a-uiimage-from-a-cgimage-in-swift)
