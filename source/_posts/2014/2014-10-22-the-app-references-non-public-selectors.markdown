---
layout: post
title: "[解決]The app references non-public selectors"
date: 2014-10-22 16:33:22 +0900
comments: true
categories: Xcode
---
アプリを申請用にValidateしようとした時のエラーめも。  
Xcode 6.1特定で出るみたいです＞＜  


> iTunes Store operation failed.  
> Your app contains non-public API usage.Please review the errors, corrent them, and resubmit your application.
>
> ----  
>
> iTunes Store operation failed.  
> The app references non-public selectors in Palyload/[APP NAME].app/[APP NAME]:allocBatch:withEntity:coount:, allocWithEntity:, firstIndexForKnownKey:, indexForKey:
>
> ----
>
> iTunes Store operation failed.  
> If you think this message was sent in error and that you have only used Apple-published APIs in accordance with the guidelines, send the app's nine-digit Apple ID, along with detailed infomation about why you believe the above APIs where incorrectly flagged, to appreview@apple.com. ....

参考：[Non-public selectors in Payload/](http://stackoverflow.com/questions/26369633/non-public-selectors-in-payload)  
現時点ではXcode6.0.1で申請を出すしかないみたいです。  

古いバージョンのXcodeのダウンロードはここから→[https://developer.apple.com/downloads/index.action](https://developer.apple.com/downloads/index.action)

## 解決策
Qiitaにも同じ[投稿](http://qiita.com/PGMY/items/831618ad416780e0dc7c)をしておいたところ、コメントで教えていただきました！  
英語なコメントにどきどき。  
英語力ないのでちゃんと理解するために友人に助けを求めつつ・・・  

結論として、  

```
/Users/{user_home_folder}/Library/Developer/Xcode/DerivedData
```

きっちりCleanする時にもよく削除したりしていますが、ここに古いバージョンのXcodeでbuildしたデータが残った状態で、新しいXcodeでArchive->Validationを行うとこのエラーが表示されてしまうみたいです。  

DerivedData内のデータを念のため丸ごと削除して行うと無事Xcode6.1でもいけました！  

PrivateAPIを使っていないのにこのエラーが出た場合はDerivedDataの削除を試してみてください！  
