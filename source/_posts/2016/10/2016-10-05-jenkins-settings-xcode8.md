---
layout: post
title: "Jenkins Settings Xcode8"
date: 2016-10-05 15:18:08 +0900
comments: true
category:
tags: []
keywords:
published: false
---

[macOS10.12 SierraにアップデートしたらうちのJenkins執事ちゃんがストライキを起こしてしまったメモ](http://pgmy.github.io/blog/2016/10/03/jenkins-macos10-dot-12-sierra/)を書いたばっかりだけれども。  
期待通りに動いていない部分があったので結局JenkinsのXcodePluginは使わずシェルスクリプト実行で行くことにしました……  



<!-- more -->

# 問題になったこと
- Auto Siging機能を使うことに寄って、Archive->ipaへExportしたファイルがDevelopmentでExportされてしまう(enterpriseにしたかった)
- enterpriseでExportするためにCustom xcodebuild argumentsでexportOptionsPlistを指定したものの、最終的にambiguousエラーでつまってしまった

Xcodeのプロジェクト設定をいろいろいじってみたものの、ambiguousエラーを解決することができなかったのと時間をかなり使ってしまったのとで最終的にシェルスクリプトでarchiveすることにしました。


# スクリプトの設定
## xcodebuildスクリプト


```
#!/bin/sh

#settings --------------------
projectname="MyProject.xcodeproj"
archivename="MyProject.xcarchive"
schemename="MyProjectScheme"
configuration="AdHoc"
#------------------------------

xcodebuild clean \
    -project $projectname\
    -configuration $configuration \
    -alltargets

xcodebuild archive \
    -project $projectname \
    -scheme $schemename \
    -configuration $configuration \
    -archivePath $archivename

xcodebuild -exportArchive \
    -archivePath $archivename \
    -exportPath ./ \
    -exportOptionsPlist ./exportOptions.plist \
    CODE_SIGN_IDENTITY="【】" \
    OTHER_CODE_SIGN_FLAGS="--keychain=/Users/username/Library/Keychains/enterprise.keychain"
```

## exportOptions.plist


```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
    <string>enterprise</string>
    <key>teamID</key>
    <string>【TEAM ID】</string>
    <key>uploadBitcode</key>
    <false/>
    <key>uploadSymbols</key>
    <true/>

</dict>
</plist>

```

【TEAM ID】はPrefix値。
