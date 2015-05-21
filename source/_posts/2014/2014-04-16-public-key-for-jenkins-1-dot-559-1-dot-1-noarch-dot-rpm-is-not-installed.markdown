---
layout: post
title: "Public key for jenkins-1.559-1.1.noarch.rpm is not installed"
date: 2014-04-16 11:34:15 +0900
comments: true
category: CentOS
tags: ["Jenkins","yum"]
keywords: Public, key, for, jenkins-1.559-1.1.noarch.rpm, is, not, installed
published: false

---


```
Public key for jenkins-1.559-1.1.noarch.rpm is not installed
```


http://pkg.jenkins-ci.org/redhat/

RPMパッケージの公開鍵をインポートしないといけなかったみたい。
rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
<!-- more -->

** Contents **
{:TOC}
