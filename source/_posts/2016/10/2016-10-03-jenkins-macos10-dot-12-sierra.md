---
layout: post
title: "macOS10.12 SierraにアップデートしたらうちのJenkins執事ちゃんがストライキを起こしてしまったメモ"
date: 2016-10-03 16:11:35 +0900
comments: true
category: Xcode
tags: [Jenkins,Xcode,iOS,Sierra]
keywords:
published: true
---

Macをアップデートしたらうちの大事な執事ちゃんがストライキを起こしたのでなだめる方法をめもめも。  
正直原因が多岐に渡っていたので、実は必要なかった設定とかがあるかもしれない…  

余計な処理とか推奨されない対応してるとかあったら教えてください偉い人Σ・ω・｀

XcodeBuilderを利用させるために四苦八苦したメモです。
brewとrubyは既にmacOS10.12に対応させるためごにょごにょしてあります。


<!-- more -->

## とりあえずJenkinsが真っ白になってしまった…
OSをアップデートしてから起動画面を見に行くと画面が真っ白に…
CSS等が読み込まれずに文字だけの表示というのは経験があったんだけども、画面全体が真っ白で何も映らない。  
接続エラーの画面ではないのでJenkins自体はちゃんと起動している様子。
Chromeで確認すると413エラーが帰ってきていたのでそのあたりで検索かけたら見つけましたほんっとうにありがとうございます！！！！！！

⇒[JenkinsでFULL head 413を返すとき](http://orca8.blogspot.jp/2014/08/jenkinsfull-head-413.html)

とりあえずクッキーを消せばどうにかなる！
ということなので、個別にCookieを編集・削除できるExtention [EditThisCookie](https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg?hl=ja) を導入しました・ω・これ、便利。  
ざざっとJenkinsページのCookieをまるまる削除したら画面が表示されるようになりましたわーい♪♪

## NullPointer??なエラー
画面が表示されるようになったのでさっそくXcodeBuildを試してみる…

```
ERROR: Build step failed with exception java.lang.NullPointerException at au.com.rayh.XCodeBuilder.perform
```

上記とともにざざざっとスタックトレースが表示されているんだけども…どうやらRubyまわりの問題みたい。
実はSierraにアップデートしてからrubyの管理をrbenvで管理するように変更したのだけれども、こちらに対応させる必要があった様子。
[Jenkinsでrubyのビルドでハマった点](http://qiita.com/kitaro_tn/items/22fd3830ba9a85ad5320)で設定方法が解説されてたのでこの通りにしました。

## resource-rules (deprecated in Mac OS X >= 10.10)! なエラー

どうやら`--resource-rules`オプションがdeprecatedになっている様子なので
[Fixing your iOS build scripts](https://www.jayway.com/2015/05/21/fixing-your-ios-build-scripts/)を参考に

```
/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/PackageApplication
```

上記ビルドスクリプト内の `@codesign_args` から `resource-rules` と `--resource-rules=$destApp/ResourceRules.plist` を削除してあげる。  
参考先は` ResourceRules.plist` が存在している場合としていない場合とで分けるように修正してくれていました＞ω＜

```
...
my @codesign_args;
    if (-e '$destApp/ResourceRules.plist') {  # If ResourceRules.plist exists, include it in codesign arguments, for backwards compatability
        @codesign_args = ("/usr/bin/codesign", "--force", "--preserve-metadata=identifier,entitlements,resource-rules",
                         "--sign", $opt{sign},
                         "--resource-rules=$destApp/ResourceRules.plist");
    } else { # If ResourceRules.plist isn't found, don't include it in the codesign arguments
        @codesign_args = ("/usr/bin/codesign", "--force", "--preserve-metadata=identifier,entitlements",
                         "--sign", $opt{sign});
    }
```

## ambiguousなエラー

```
error: /usr/bin/codesign --force --preserve-metadata=identifier,entitlements --sign iPhone Distribution: [証明書] --entitlements /var/folders/_c/xblnly_j59n4vmbhq061q_g00000gp/T/W_1s7LZRB8/entitlements_plist3tPGS55p /var/folders/_c/xblnly_j59n4vmbhq061q_g00000gp/T/W_1s7LZRB8/Payload/MEME-Run-iOS.app failed with error 1. Output: iPhone Distribution: [証明書]: ambiguous (matches "iPhone Distribution: [証明書]" in /Users/[ユーザー名]/Library/Keychains/login.keychain-db and "iPhone Distribution: [証明書]" in /Library/Keychains/System.keychain)
```

これは通常証明書が２つ存在している場合等に表示されるエラー。使っている証明書と期限切れの証明書がまだキーチェーンの中に入っている場合は削除すれば問題ない。  
Xcode8からはAuto Siging機能が追加されているので、[What's New in Xcode App Signing](http://dev.classmethod.jp/smartphone/xcode-8-signing/)を参考に、プロビジョニング等の設定を全てAutoに変更し、Jenkins内のXcodeBuilder設定の証明書の記述も全て削除してしまえば問題なくビルドできるようになりました。

ふあーーーすっきり。
ちょっと対応が多すぎて実際必要だったのがどれやら…
とりあえず整理のためにめもめもしておきます。
