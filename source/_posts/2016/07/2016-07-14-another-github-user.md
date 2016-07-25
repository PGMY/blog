---
layout: post
title: "１つの端末で２つのGithubアカウントを利用する"
date: 2016-07-14 12:52:22 +0900
updated: 2016-07-25 10:57:25 +0900
comments: true
category: Snippet
tags: [Github,Git,RSA,SSH]
keywords:
published: true
---

毎回手順を忘れるのでメモしておくことにした

## SSH Key問題
Githubだと別のアカウントで同じSSH Keyを利用できないので、１つの端末で別のgithubアカウントを同時に利用しようと思うとちょっと工夫が必要な感じ。  

## アカウント別のSSH Keyを作成する
普通に・ω・  

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

ファイル名はデフォルトのままではなくて、そのアカウントとわかる形の名前にしておくのがいい。  

## .ssh/config設定
configに接続情報を記載する。  
Host名をgithub.comじゃなくてそのアカウント用と分かるような名前をつけてあげる。  
HostNameはgithub.comのまま。  
IdentityFileで作成したkeyへのパスを指定する。  

```
Host github-another-user
  User git
  Port 22
  HostName github.com
  IdentityFile ~/.ssh/another_rsa
  TCPKeepAlive yes
  IdentitiesOnly yes
```

## 接続する
通常の `git@github.com:PGMY/ProjectName.git` で接続するんじゃなくて、configで指定したhost名を書き換えて接続する。  

```
git@github-another-user:PGMY/ProjectName.git
```

これでおけΣｄ・ω・
