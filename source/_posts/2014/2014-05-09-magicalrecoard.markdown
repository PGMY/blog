---
layout: post
title: "magicalrecoard"
date: 2014-05-09 09:37:43 +0900
comments: true
category: magicalrecoard
tags: []
keywords: magicalrecoard
published: false

---


https://github.com/PGMY/MagicalRecord

| バージョン | 内容 |
|--------|--------|
| 1.8.3 | ARC使わないタイプ
| 2.0 | ARC対応
| 2.1 | メソッド名とかがいろいろ変わった

## 基本的な用語

### DataModel データモデル
スキーマみたいなもの

### Entity エンティティ
テーブルみたいなもの  
独自のManaged Objectの定義  
実態はXML

### Atribute 属性
カラムみたいなもの


# MagicalRecordを使う

## 初期化
保存方法を指定する

[MagicalRecord setupCoreDataStack];

[MagicalRecord setupCoreDataStackWithAutoMigratingSqliteStoreNamed:@"db.sqlite"];


<!-- more -->

** Contents **
{:TOC}
