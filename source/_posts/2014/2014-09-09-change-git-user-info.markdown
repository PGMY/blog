---
layout: post
title: "gitのコミット履歴のユーザー情報を一括変更する"
date: 2014-09-09 11:36:45 +0900
comments: true
category: git
tags: [git]
keywords: gitのコミット履歴のユーザー情報を一括変更する
published: true
---
過去のコミット履歴にあるユーザー情報をまとめて一括変更する方法を調べたのでめもも。  

```
git filter-branch -f --commit-filter '
        if [ "$GIT_AUTHOR_NAME" = "【変更したいユーザー名】" ];
        then
                GIT_AUTHOR_NAME="【変更後ユーザー名】";
                GIT_AUTHOR_EMAIL="【変更後メールアドレス】";
                git commit-tree "$@";
        else
                git commit-tree "$@";
        fi' HEAD
```
