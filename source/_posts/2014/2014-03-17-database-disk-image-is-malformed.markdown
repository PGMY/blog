---
layout: post
title: "database disk image is malformed"
date: 2014-03-17 14:43:53 +0900
comments: true
category: database
tags: ["disk", "image", "is", "malformed"]
keywords: database, disk, image, is, malformed
published: false

---

ある日突然

```
database disk image is malformed
```

と、おこられてしまいました；ω；

ひとまず対処法だけ記載。

```
yum clean dbcache
```

もしくは

```
yum clean all
```

とすればいいみたい・ω・
