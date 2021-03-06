---
layout: post
title: "wordpressでの改行問題"
date: 2016-08-05 11:46:43 +0900
updated: 2016-08-09 13:28:11 +0900
comments: true
category: WordPress
tags: []
keywords:
published: false
---

# WordPressで自由に行間が開けられない問題について
ブログを書いていて行間を調整しようと思った時に、行間を調整する場合改行や `<br />` タグを利用してかくのが手っ取り早いです。  
ので、やってしまいがちですが、WordPressで連続改行するとなるといくつかの問題がでてきます。  
今回クライアントから"改行を入れることで自由に行間を調整したい"依頼があり、どうにかしてくれということだったのでいろいろ調べてメモがてらまとめてみた。  
※残念ながらWordPressやPHPは本業ではなく、がっつりいろいろやってるわけではなくて、イレギュラー的に頼まれた案件なので知識不足諸々多いです；ω；  

難しいことにこの改行のためにいろいろな問題が一緒に浮上しているためやりたいことシンプルですが１手のみで解決できるものではなく…ややこしくなったので整理したい所存です。  
今回の案件で問題になった部分と要望は下記になります。

- 投稿者が複数人存在し、テキストエディタで投稿する者とビジュアルエディタで投稿する者が存在している。その中で相互の切り替えをした場合に改行が消えてしまう。  
- カスタムフィールドでエディタを追加しているが、そもそも切り替えタブが表示されてくれない。  
- TinyMCEを以前導入したがわけわかめ状態になったので停止中なう。できれば今回も使いたくはない。

知らんがな！！  
とか言ってみたい人生でした…  

<!-- more -->

# 行間を開けるための推奨される記述について
そもそもHTMLでは改行タグは連続して使うものではないそうです。  
はっ！？そうなのか…調べるまで知らなかった…。余裕で改行で行間調整してました…orz  
そのため、WordPressのビジュアルエディタ等で改行を連続して使うと、何度改行しようとも１回分となるように調整してくれます。便利です。  
が、正直面倒ですｗ  

そのため、改行タグを連続して入力できるようにするプラグインが存在してたりします。  
私もWordPressで少しばかりブログを書いたりしたことがありましたが、必須のプラグインでした。  

## よくある解決案１:brBrbrプラグインの導入

- [WordPress 改行プラグイン](http://camcam.info/wordpress/101)

思い通りに改行できる、必須のプラグインとして様々なところで紹介されていますが、残念ながら公式ディレクトリでは配布されておらず、ダッシュボード内から検索しても出てきません。  
そのため、上記サイトからダウンロードしてインストールする必要があります。  
公式ディレクトリで配布されない理由は"WordPressのコアファイルを改変するものだから"や"連続改行がそもそも推奨された記述ではないから"等があるようです。  

便利なのになー…ちぇっ。  

とうことで今回こちらは見送ってみます。  

## よくある解決案２:スペース( `<br />&nbsp;` )を入れる
こちらもよく利用される方法。 `<br />` タグや、ビジュアルエディタの改行と共に、半角スペース `&nbsp;` を入力して改行タグが消えないように制御する方法。  
`&nbsp;` は正確には半角スペースではなく、 `Non-Breaking SPace` のことです。  
ここでは改行しないでね！の意味で利用されるスペースのようで、これを入れることによって、文字列が入力されていると認識され連続改行ではないと判断されます。  
文字列といっても見た目では空白が入っているだけに見えるのでただの改行に見えるという寸法！！

## よくある解決案３:TinyMCEの導入


### 参考
- [犯してはいけないHTMLタグの過ち10個](http://weboook.blog22.fc2.com/blog-entry-254.html)
- [WordPressでプラグイン使わず連続改行させる素直な方法](http://www.02320.net/repeat_break_without_plugin/)
- [連続改行brはNG！？覚えておいて損はない改行と段落の使い分け方](http://naifix.com/br/#WordPress)
- [ビジュアルエディタの改行の仕組み、理解してますか？](http://www.nishi2002.com/18740.html)

# タグの自動変換を無効にする




### 参考
- [WordPressの自動整形（ビジュアルエディタ含む）を無効にする方法](http://qiita.com/jyokyoku/items/c560b0d1eacc1df61620)



# プラグイン

add_action('save_post', 'costumize_br');
function force_update_post() {
}


# TinyMCE 切り替えjsのカスタマイズ
[[WordPress] エディタ（TinyMCE）の自動整形をカスタマイズする](http://www.560designs.com/memo/839.html)



### 参考
- [改行プラグイン(my_autop)を改良してみた](http://plime.s329.xrea.com/plime/tech/2011/01/09/251/)



## カスタムフィールドでのエディタ切り替え問題
カスタムフィールドで追加したwysiwygエディタだとそもそもテキストエディタとビジュアルエディタの切り替えボタンが表示されない。  
これは便利なプラグインを作成してくくれてた方がいるので、下記を導入すれば解決！  

<div class="github-widget" data-repo="elliotcondon/acf-wordpress-wysiwyg-field"></div>

### 参考
- [カスタムフィールドでビジュアルエディタとテキストエディタを使う](http://notes25.net/wordpress/%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%A0%E3%83%95%E3%82%A3%E3%83%BC%E3%83%AB%E3%83%89%E3%81%A7%E3%83%93%E3%82%B8%E3%83%A5%E3%82%A2%E3%83%AB%E3%82%A8%E3%83%87%E3%82%A3%E3%82%BF%E3%81%A8%E3%83%86%E3%82%AD/)


## 新しいWordPressでエディタの切り替えができなくなる問題
これは「PS Disable Auto Formating」というプラグインを導入していた場合に起こる問題の模様。  
pタグやbrタグが自動整形で消えてしまう問題を解決する為のプラグインで導入しているユーザーが多い。  
一部メソッドが機能しなくなってエラーが起こっているのが原因なので、ひとまず止めてしまえば切り替えはできるようになるけど、本来欲しかった機能が消えてしまう…
### 参考
- [WordPressでビジュアルとテキストが切り替わらない！問題のプラグインと対処法](http://nozziedesign.com/1987.html)
<!-- more -->
