---
layout: post
title: "Jenkins plugin"
date: 2016-10-14 10:47:41 +0900
comments: true
category:
tags: []
keywords:
published: false
---


```
brew install maven
```


```
<settings>
  <pluginGroups>
    <pluginGroup>org.jenkins-ci.tools</pluginGroup>
  </pluginGroups>

  <profiles>
    <!-- Give access to Jenkins plugins -->
    <profile>
      <id>jenkins</id>
      <activation>
        <activeByDefault>true</activeByDefault> <!-- change this to false, if you don't like to have it on per default -->
      </activation>
      <repositories>p
        <repository>
          <id>repo.jenkins-ci.org</id>
          <url>http://repo.jenkins-ci.org/public/</url>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <id>repo.jenkins-ci.org</id>
          <url>http://repo.jenkins-ci.org/public/</url>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
</settings>
```


## サンプルプロジェクトの作成

```
mvn -cpu hpi:create
```

```
mvn package
```

```
mvn install
```






<!-- more -->



- [コマンドラインから LINE にメッセージを送れる LINE Notify](http://developers.linecorp.com/blog/ja/?p=3784)
- [Plugin tutorial](https://wiki.jenkins-ci.org/display/JA/Plugin+tutorial)
- [Jenkinsプラグインを作ってみる(1) - セットアップとサンプルコード -](http://qiita.com/tarappo/items/d8dbe2828ada53979bbf)
- [Jenkinsプラグインを開発する](http://qiita.com/kazuqqfp/items/ded99eb8d7bd967b9d2a)
- [Jenkinsプラグイン開発　Notifier拡張のプラグインを作る　その１](http://kaakaa.hatenablog.com/entry/20120701/1341127309)
