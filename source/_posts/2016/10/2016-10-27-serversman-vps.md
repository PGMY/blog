---
layout: post
title: "Serversman vps"
date: 2016-10-27 12:37:46 +0900
comments: true
category:
tags: []
keywords:
published: false
---
<!-- more -->


OS : CentOS 7 (64bit)  
HDD : 100GB  
メモリ : 2GB + 1GB(オプション)  
パッケージ : シンプルセット  


### SSH接続

```
$ ssh -p [ポート番号] root@[IPアドレス]
```

### 管理ユーザー追加とパスワード設定

```
# useradd -G wheel [ユーザー名]
# passwd [ユーザー名]
ユーザー [ユーザー名] のパスワードを変更。
新しいパスワード:
新しいパスワードを再入力してください:
passwd: すべての認証トークンが正しく更新できました。
#
```

### sudoユーザーの追加とwheelグループの設定の確認

```
# visudo

```

```
%wheel ALL=(ALL) ALL
```

### ログイン設定についての設定

```
# vi /etc/ssh/sshd_config
```

Rootでのログインを禁止して接続するユーザーを設定する

```
#PermitRootLogin yes
PermitRootLogin no

AllowUsers [ユーザー名]
```

### SSH再起動

```
service sshd restart
```

※ここで別ウィンドウで追加したユーザーでアクセスできることとrootでアクセス出来ないことを確認しておく



# 初期設定
### yumのアップデート

```
yum --skip-broken update
```

エラー
```
エラー:  Multilib version problems found. This often means that the root
        cause is something else and multilib version checking is just
        pointing out that there is a problem. Eg.:

          1. You have an upgrade for openldap which is missing some
             dependency that another package requires. Yum is trying to
             solve this by installing an older version of openldap of the
             different architecture. If you exclude the bad architecture
             yum will tell you what the root cause is (which package
             requires what). You can try redoing the upgrade with
             --exclude openldap.otherarch ... this should give you an error
             message showing the root cause of the problem.

          2. You have multiple architectures of openldap installed, but
             yum can only see an upgrade for one of those architectures.
             If you don't want/need both architectures anymore then you
             can remove the one with the missing update and everything
             will work.

          3. You have duplicate versions of openldap installed already.
             You can use "yum check" to get yum show these errors.

        ...you can also use --setopt=protected_multilib=false to remove
        this checking, however this is almost never the correct thing to
        do as something else is very likely to go wrong (often causing
        much more problems).

        Protected multilib versions: openldap-2.4.40-8.el7.x86_64 != openldap-2.4.39-3.el7.x86_64
```

これは `openldap` をアップデートしてあげてからやれば大丈夫でした。



# Apache
## テストページを表示させない設定

```
vi /etc/httpd/conf.d/welcome.conf
```

```
<LocationMatch "^/+$">
   Options -Indexes
    ErrorDocument 403 /error/noindex.html
</LocationMatch>
```
これを全てコメントアウト

## ディレクトリ一覧を表示させない

```
vi /etc/httpd/conf/httpd.conf
```

```
Options Indexes FollowSymLinks
  ↓
Options -Indexes +FollowSymLinks
```
