---
layout: post
title: "Octopress+Travis CI"
date: 2014-07-03 11:56:41 +0900
comments: true
category: Octopress
tags: ["TravisCI","Octopress"]
keywords: Octopress+Travis, CI
published: true

---
OctopressをTravis CIを使って自動デプロイに挑戦してみましたΣ  
なんとも試行錯誤しまくるハメになったものの、[Octopress + GitHub Pages + Travis](http://blog.yasuoza.com/2014/01/13/octopress-plus-github-pages-plus-travis/)の通りにやってやっとこさうまく行きました。  
はじめはトークンを利用しての方法にしてたんですがうまく行かず、結局公開鍵使ってのデプロイですー。  

<!-- more -->

## Step 1 - travisコマンドをインストール

```
gem install travis
```
これをたたけばオッケーです。  
環境によっては`sudo`権限必要ですよーう！

## Step 2 - TravisCI用の鍵を作って登録します

```
# 鍵を作成する
ssh-keygen -t rsa -C 'youremail@examp.com' -f ~/.ssh/travis_rsa
# クリップボードにコピーする
cat ~/.ssh/travis_rsa.pub | pbcopy
```

`https://github.com/ユーザー名/リポジトリ名/settings/keys`  
上記URLからデプロイキーを登録します

## Step 3 - Proseのための設定を追加する
`_config.yml`に下記をそのまま追加させてもらいました

```
#prose.io settings
prose:
  rooturl: "source"
  metadata:
    "source/_posts":
      - name: "layout"
        field:
          element: "hidden"
          value: "post"
      - name: "title"
        field:
          element: "text"
          value: "Title"
      - name: "comments"
        field:
          label: "Allow comments"
          element: "checkbox"
          value: true
      - name: "categories"
        field:
          element: "text"
          value: "misc"
      - name: "published"
        field:
          label: "Published"
          element: "checkbox"
          value: true
```
Proseから編集する際のルートとなるディレクトリと編集できるファイルを設定します。  
詳細は[Prose Configuration](https://github.com/prose/prose/wiki/Prose-Configuration)に記載されているようですが、私は[参考先](http://rogerz.github.io/blog/2013/02/21/prose-io-github-travis-ci/)のデータをそのまま利用させてもらいました。

## Step 4 - TravisCIのための設定を追加する

`.travis.yml`を下記のように書きます

```
language: ruby
rvm:
- 2.1.0
branches:
  only:
  - master
before_script:
- git config --global user.name "ユーザー名(via Travis CI)"
- git config --global user.email "メールアドレス"
- git remote set-url origin $REPO.git
- if [ -z "$id_rsa_{1..23}" ]; then echo 'No $id_rsa_{1..23} found !' ; exit 1; fi
- echo -n $id_rsa_{1..23} >> ~/.ssh/travis_rsa_64
- base64 --decode --ignore-garbage ~/.ssh/travis_rsa_64 > ~/.ssh/id_rsa
- chmod 600 ~/.ssh/id_rsa
- echo -e "Host github.com\n\tStrictHostKeyChecking no\n" >> ~/.ssh/config
- bundle exec rake setup_github_pages[$REPO]
- git checkout -- _config.yml
script:
- bundle exec rake generate
after_script:
- bundle exec rake deploy
env:
  global:
  - REPO="git@github.com:ユーザー名/リポジトリ"
```

## Step 5 - 鍵の暗号化
base64で鍵を暗号化します。

```
# Macを使っている場合はこれで。
$ base64 --break=0 ~/.ssh/travis_rsa > ~/.ssh/travis_rsa_64

# Linuxの場合はこっちを使います
# base64 --wrap=0 ~/.ssh/travis_rsa > ~/.ssh/travis_rsa_64

$ bash <(cat ~/.ssh/travis_rsa_64 | perl -pe 's/(.{100})/$1\n/g' | nl | perl -pe 's/\s*(\d+)\s*(.*)/travis encrypt id_rsa_$1="$2" --add/')
```

`.travis.yml`内の`$id_rsa_{1..23}`部分の`23`の個数を数えます

```
cat ~/.ssh/travis_rsa_64 | perl -pe 's/(.{100})/$1\n/g' | nl | tail
```

```
...
21	ZktBVTJMZHFZajA0UUpFZnQvd01mblRDTWQKRVBMdHF3QU9rM05wOUh5T3V0MlZ1SnREa3VHVU14dUlKWWcvT0JDU0thSEhUMW1P
22	bWJ5eSt5QmtUdkdiQkx3OApPT29lRFBzLzRwUEpibjN5eTVCemk2TEtLK3hwRHBzYUg5MHZwUkg3WXdRd3NLQUNvdElLNHdzPQot
23	LS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
...
```

こんな感じに出力されるので、左側の数字を使います。  

## Step 6 - Rakefileを修正

```
-  cd "#{deploy_dir}" do
-    system "git pull"
+  cd "#{deploy_dir}" do
+    system "git pull origin #{deploy_branch}"
```

これでオッケーでした！！パチパチ。


## 参考
- [Octopress+Prose+Github+Travis CI = Coders' Blog](http://rogerz.github.io/blog/2013/02/21/prose-io-github-travis-ci/)
- [OctopressをTravis CIでデプロイするやつ](http://gutti.jp/blog/2014/02/19/octopress-travis-ci/)
- [OctopressとTravis CIを連携させてBlog生成を自動にする](http://pchw.github.io/blog/2013/06/27/octopress-travis/)
- [Octopress + GitHub Pages + Travis](http://blog.yasuoza.com/2014/01/13/octopress-plus-github-pages-plus-travis/)
