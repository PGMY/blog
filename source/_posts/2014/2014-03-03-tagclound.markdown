---
layout: post
title: "Octopressでタグクラウドを表示するプラグインを導入してみた"
date: 2014-03-03 14:12:50 +0900
comments: true
category: Pctopress
tags: [TagCloud,Plugin]
keywords: tagclound
published: true
---
OctoPressでタグクラウドを表示するプラグインを導入してみたので作業記録を・・・。

利用したのはこちらのプラグインです。
<div class="github-widget" data-repo="tokkonopapa/octopress-tagcloud"></div>

<!-- more -->

カテゴリー部分をタグクラウドとして実現するプラグインですが、タグとカテゴリーは分けて使いたいな・・・と思ったらこんな記事を見つけました！

[Octopressでのタグの運用](http://rcmdnk.github.io/blog/2013/04/12/blog-octopress/)

上記記事の通り上から下まで設定させていただきました・ω・ありがとうございます！

で、残念ながらtag_croud.rbでうまく動作しない部分があったのでそこも修正しました。

- タグのリンクが２重になってしまう　tags/tagcloudtagcloud
- タグのページに移動するとタグ部分のリンクがさらに２重になってしまう　tags/tagcloudtagcloudtags/tagcloudtagcloud

みたいな感じになってしまったので修正しました。
設置している環境によるのかも。

```
tag_dir = config['root'] + config['category_dir'] + '/'
　↓
tag_dir = config['tag_dir'] + '/'
```

```
html = ''
　↓
html = ""
```

Rubyは詳しくないんですが、シングルコーテーションだと初期化されないのかな？ダブルコーテーションに変更しました。

これで思った通りに動くようになりました・ω・

ありがたく使わせていただきます＞ω＜ありがとうございます！