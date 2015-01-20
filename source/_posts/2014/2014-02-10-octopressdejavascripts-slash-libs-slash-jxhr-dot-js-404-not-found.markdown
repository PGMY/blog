---
layout: post
title: "Octopressでjavascripts/libs/jXHR.js 404 (Not Found)"
date: 2014-02-10 15:41:04 +0900
comments: true
categor: JavaScript
tags: [Octopress,Error]
---

Octopressを設置するとコンソールにエラー出力が。

```
GET http://pgmy.github.io/javascripts/libs/jXHR.js 404 (Not Found) 
```

このブログをおいているのが`http://pgmy.github.io/blog`なので正しくは

```
http://pgmy.github.io/blog/javascripts/libs/jXHR.js
```
にならないといけないところ。

検索すると同様の問題が出てきたのでメモ。
- [Octopress Finally Working](http://giskou.com/blog/octopress-finally-working/)
- [jXHR in github.html has wrong URL #221](https://github.com/imathis/octopress/issues/221)  

```
jxhr.src = '{{root_url}}/javascripts/libs/jXHR.js'
```

となっている箇所を

```
jxhr.src = '{{site.root}}/javascripts/libs/jXHR.js'
```

へ変更する

これでオッケーΣd・ω・