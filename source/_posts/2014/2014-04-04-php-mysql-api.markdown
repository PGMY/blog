---
layout: post
title: "PHPでのMySQL接続APIを選ぶ"
date: 2014-04-04 11:20:28 +0900
comments: true
category: PHP
tags: [MySQL,API]
keywords: php-mysql-api
published: true

---

PHPを勉強しはじめたところでMySQLへの接続部分で疑問だらけになったので整理しつつメモしていきます。

# MysqliとPOD_MySQL

- 参考：[MySQL用PHPドライバの概要 > どのAPIを使うか](http://us1.php.net/manual/ja/mysqlinfo.api.choosing.php)  

<!-- more -->

いろんなサイトでMySQLへの接続は`mysql_connect`を利用すると書かれているものの、マニュアルサイトを見たら

> この拡張モジュールはPHP5.5.0で非推奨になりました。将来のバージョンで削除される予定です。 [MySQLi](http://www.php.net/manual/ja/book.mysqli.php)あるいは[PDO_MySQL](http://www.php.net/manual/ja/ref.pdo-mysql.php)を使うべきです。詳細な情報は [MySQL:APIの選択](http://www.php.net/manual/ja/mysqlinfo.api.choosing.php)やそれに[関連するFAQ](http://www.php.net/manual/ja/faq.databases.php#faq.databases.mysql.deprecated)を参照ください。この関数の代替として、これらが使えます。

との警告が赤字ででてました。なるほど。  
FAQの方には

> 広く使われている拡張モジュールなので、ある日突然消えてしまうなどということはないでしょう。でも、今後新しく書くコードではmysqliかPDO_MySQLを使うことを強く推奨します。

と書かれていました。私はこれから勉強するので存在は知りつつも、実際に勉強して使っていくのは`mysqli`か`PDO_MySQL`ってことでいいのかな。  
じゃあどっち使えばいいのよ！となるわけで。  

とりあえず比較表がでてました。

|  | 　ext/mysqli　 | 　PDO_MySQL　 | 　ext/mysql　 |
|:-----------|:------------:|:------------:|:------------:|
| どのバージョンの PHP から使えるか | 5.0 | 5.1 | 2.0
| PHP 5.x に同梱されているか | Yes | Yes | Yes
| 開発状況 | 進行中 | 進行中 | 保守対応のみ
| ライフサイクル | 活動中 | 活動中 | 非推奨
| 新規開発でおすすめできるか | Yes | Yes | No
| オブジェクト指向のインターフェイス | Yes | Yes | No
| 手続き型のインターフェイス | Yes | No | Yes
| mysqlnd によるノンブロッキングな非同期クエリ | Yes | No | No
| 持続的接続 | Yes | Yes | Yes
| 文字セット | Yes | Yes | Yes
| サーバーサイドのプリペアドステートメント | Yes | Yes | No
| クライアントサイドのプリペアドステートメント | No | Yes | No
| ストアドプロシージャ | Yes | Yes | No
| 複数ステートメント | Yes | Most | No
| トランザクション | Yes | Yes | No
| SQL によるトランザクション制御 | Yes | Yes | Yes
| MySQL 5.1+ の全機能への対応 | Yes | Most | No


ほむ。わからない言葉がいろいろと。  

## オブジェクト指向と手続き型

このあたりの違いは下記サイトを見るととてもわかりやすいです。

- 参考：[手続き型とオブジェクト指向の違いをWebアプリで理解する](http://quill3.hatenablog.com/entry/20090321/p1)

MySQLの例ではありませんが。  
両方使えるmysqliで見てみると、

- 手続き型

```
$db = mysqli_connect("example.com", "user", "password", "database");
$result = mysqli_query($db,"SELECT 'Hello, dear MySQL user!' AS _message FROM DUAL");
$row = mysqli_fetch_assoc($result);
echo htmlentities($row['_message']);
```

- オブジェクト指向

```
$mysqli = new mysqli("example.com", "user", "password", "database");
$result = $mysqli->query("SELECT 'Hello, dear MySQL user!' AS _message FROM DUAL");
$row = $result->fetch_assoc();
echo htmlentities($row['_message']);
```

こんな感じになるのかなぁ・・・。  
基本的には手続き型は使わない方向でいこうと思っているのでmysqliでもPDO_MySQLでもどちらでも大丈夫という結論に・ω・

## mysqlnd によるノンブロッキングな非同期クエリ？

ひとまずmysqlndってなんだ！　⇒　MySQL Native Driver For PHPのことだそうです。  

- 参考：[MySQL native driver for PHP(通称mysqlnd)だ](http://d.hatena.ne.jp/bco/20110101/1293887044)  

> mysqlndの利点は2つある。ひとつはパフォーマンス。オリジナルのmysql(i)拡張およびPDO_MYSQLよりも速い。そしてライセンス。
> mysqlndはGPLv2ではなくPHPライセンスなのだ。

パフォーマンスが良くなるってことなのかな？ライセンスについてはマニュアルに詳しく記載されてます。  

- 参考：[PHPマニュアル>mysqlnd>概要]

おすすめ機能として下記があげらえていました。

> MySQL Native Driver には、それ特有の機能もあります。 以下にまとめるこれらの機能は、MySQL Client Library を使った MySQL データベース用の拡張モジュールでは利用できないものです。
> - 持続的接続の改善
> - mysqli_fetch_all() 関数
> - パフォーマンス統計用の関数 mysqli_get_cache_stats()、 mysqli_get_client_stats()、 mysqli_get_connection_stats()  
> パフォーマンス統計機能を活用すれば、 パフォーマンス上のボトルネックを見つけるのに大いに役立つでしょう。
>
> MySQL Native Driver を mysqli 拡張モジュールで使うと、持続的接続も可能です。
> - SSL サポート  
> MySQL Native Driver は PHP 5.3.3 以降で SSL をサポートします。
> - 圧縮プロトコルのサポート  
> PHP 5.3.2 以降では、MySQL Native Driver は圧縮したクライアントサーバープロトコルをサポートします。 5.3.0 や 5.3.1 の時点ではまだ対応していませんでした。 ext/mysql や ext/mysqli では、MySQL Native Driver を使うように設定すればこの機能を使えるようになります。 ただし、PDO_MYSQL については mysqlnd と組み合わせても圧縮機能が使えないことに注意しましょう。

つまりPDO_MYSQLはmysqlndの圧縮機能が使えない・非同期クエリが使えない、ということでいいのかな？  
非同期クエリについては下記に詳しく記載されていました。

- 参考：[PHPの非同期クエリで並行処理をやってみる](http://fukaoi.org/2012/03/31/php_mysqli)

## 持続的接続

- 参考：[mysql_pconnect: MySQL サーバへの持続的な接続をオープンする](http://www.phppro.jp/phpmanual/php/function.mysql-pconnect.html)
- 　　：[持続的データベース接続](http://www.phppro.jp/phpmanual/php/features.persistent-connections.html)

> mysql_pconnect()は、 mysql_connect()とよく似た動作をしますが、 2 つの大きな違いがあります。  
> 1. 1 番目の違いとして、この関数は接続時にまず 同じホスト、ユーザ名、パスワードを有する(持続的)リンクが すでにオープンされていないかどうかを調べます。 それがみつかった場合、新規の接続をオープンする代わりに そのリンクの ID が返されます。  
> 1. 2 番目の違いは、スクリプトの実行が終了しても SQL サーバとの接続が 閉じられないということです。そのかわりに、将来再利用されるために リンクがオープンされたままとなります（mysql_close() は、mysql_pconnect() によって確立されたリンクを 閉じません）。  
>
> このため、この型のリンクは、'持続的(persistent)'であると言われます。

つまり、同じホスト、ユーザ名、パスワードを有する場合、スクリプトの実行が終了してもSQLサーバとの接続が閉じられない・・・？  
いろいろ説明を読むと、注意すべき点が多くあるようで、少し難しい内容でした。  
まだいろいろ理解しきれていないため、この機能の利用は保留にしようと思います。



## プリペアドステートメント

- 参考：[PHPBook プリペアドステートメント](http://www.phpbook.jp/pear/pear_db/index12.html)
- 　　：[今夜こそわかる安全なSQLの呼び出し方　～ 高木浩光氏に聞いてみた](http://www.atmarkit.co.jp/fsecurity/column/ueno/60.html)
- 　　：[PDOで接続、SELECT、プリペアドステートメントとは（PHPでMySQLに接続）](http://qiita.com/tabo_purify/items/d1166236f3b03c7be60d)

> SQLの実行は、SQL文を構文解析する作業と、それを解釈する作業に分かれる。プリペアド・ステートメントというのは、この構文解析をDBMS側で先に済ませておき、そこにパラメータを当てはめて繰り返し使うことで、処理効率を高めるために活用されるものだ。  
> プリペアド・ステートメントを使用していれば、どんなパラメータが与えられても構文解析は変わらないので、結果的にSQLインジェクションによってSQL文の構文が破壊されることは起きない。


- 構文解析作業を先にすませておける。
- パラメータを当てはめて使うことで処理効率が良くなる。
- SQLインジェクションで構文が破壊されることが起きない。(そのままw)

ということみたい。なるほどー。これは使わない手はない！  
ただ、サーバーサイドとクライアントサイドのプリペアドステートメントというのがあるそうだけど、この違いがいまいちわからない。  
mysqliがクライアントサイドのプリペアドステートメントをサポートしていない。  
ので、mysqliを使う時はここを調べないといけないけどわからないー＞△＜教えて偉い人！

## ストアドプロシージャ

- 参考：[MySQL ストアドプロシージャ](http://kozy.heteml.jp/pukiwiki/index.php?MySQL%20%A5%B9%A5%C8%A5%A2%A5%C9%A5%D7%A5%ED%A5%B7%A1%BC%A5%B8%A5%E3)  
- 　　：[mysqlのストアドプロシージャの便利さをアピールしてみる ](http://blog.livedoor.jp/sasata299/archives/51180619.html)
- 　　：[ストアドプロシージャでシステムを構築するとDBサーバの負荷が増えるか](http://d.hatena.ne.jp/Sikushima/20100610/1276153844)

> ■ストアドプロシージャとは？  
> 一連のSQL文や処理を名前をつけてサーバ側に保存し、関数呼び出しのように使用できるもの。  
> ■ストアドプロシージャを使うメリットは？  
> - アプリ側の言語を選ばない。（MySQLで実装されていて、呼ぶだけなので）  
> - アプリサーバの負荷が減る。（複雑な処理はMySQL側で実行されているため）  
>
> よく似たのに、ストアドファンクションってのもあります。  

ほむほむ・・・SQL文とか処理を保存しておけて、関数みたいに呼び出すことができると。便利そう！  
これはどちらでもサポートしているものみたい。  

## 複数ステートメント

- 参考：[最近知ったSQL〜複数のINSERTとUPDATE〜](http://freestyle.nvo.jp/archives/260)  

これもちょっといまいちわからないままでした・・・。  
とりあえず？どちらも利用できるようですが、INSERTやUPDATEを複数行まとめて行えるものなのか・・・。  
似た名前のものに[複合ステートメント](http://dev.mysql.com/doc/refman/5.1-olh/ja/begin-end.html)というものがあるようで、これとはまた別なのか？  
と、ちょっぴり怪しいままです・・・。  

## トランザクション/SQLによるトランザクション制御

- 参考：[MySQL トランザクション](http://wiki.bit-hive.com/tomizoo/pg/MySQL%20%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3)  

> トランザクションを使うと複数のクエリをまとめて１つの処理として扱うことができる。処理の途中でエラーになって処理を取り消したいような場合はROLLBACKをすることで変更内容を元に戻すことができる。

トランザクションは元々しっていたので大丈夫でした。  
注意すべきはMyISAMでは利用できず、InnoDBにしておく必要がある、ということでしょうか。  
これは両方で利用することができます。重要な機能！



ざっと調べたものをまとめただけですが。内容に誤りがあったり、理解できなかった部分がわかった時にはまた更新する予定です・・・。
