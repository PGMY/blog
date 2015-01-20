---
layout: post
title: "AirLocate"
date: 2014-04-03 09:29:31 +0900
comments: true
category: iOS
tags: [iBeacon,Sample]
published: true
---

iOS Dev Library にあるサンプルを読んでいきますー・ω・  
iBeaconに関するサンプル  

[AirLocate: Using CoreLocation to monitor, range, and configure your device as an iBeacon](https://developer.apple.com/library/ios/samplecode/AirLocate/Introduction/Intro.html)

<!-- more -->

## ファイル

- APLUUIDViewController.h / APLUUIDViewController.m  

- APLMonitoringViewController.h / APLMonitoringViewController.m  

- APLRangingViewController.h / APLRangingViewController.m  
  観測領域内のビーコンを検出する
  [CLBeaconRegion](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLBeaconRegion_class/Reference/Reference.html)を生成

- APLCalibrationBeginViewController.h / APLCalibrationBeginViewController.m  

- APLCalibrationValculator.h / APLCalibrationValculator.m  

- APLCalibrationEndViewCntroller.h / APLCalibrationEndViewCntroller.m  

- APLConfigurationViewController.h / APLConfigurationViewController.m  

- APLDefaults.h / APLDefaults.m  
  共通データを記載しているシングルトン

- APLProgressTableViewCell.h / APLProgressTableViewCell.m  
  テーブルビュー用セル

- APLAppDelegate.h / APLAppDelegate.m  
  アプリケーションデリゲート。  
  [CLLocationManager](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html)クラスを生成。  
  [CLLocationManagerDelegate](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManagerDelegate_Protocol/CLLocationManagerDelegate/CLLocationManagerDelegate.html)デリゲートを持つ。


## クラス

- [CLLocationManager](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html)  
  位置情報クラス。GPS・電子コンパス等。
  - startMonitoringForRegion:  
    領域観測イベントが発生した時のイベントのハンドリングを開始
  - startRangingBeaconsInRegion:  
    Beacon領域への出入りのイベントのハンドリングを開始
- [CLBeaconRegion](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLBeaconRegion_class/Reference/Reference.html)


## デリゲート

- [CLLocationManagerDelegate](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManagerDelegate_Protocol/CLLocationManagerDelegate/CLLocationManagerDelegate.html)  
  位置情報に関するデリゲートクラス。
  - \- (void)locationManager:(CLLocationManager *)manager didDetermineState:(CLRegionState)state forRegion:(CLRegion *)region  
    iBeacon監視状態を知らせてくれるメソッド。`CLLocationManager requestStateForRegion:`で追加すると呼ばれる
  - \- (void)locationManager:(CLLocationManager *)manager didRangeBeacons:(NSArray *)beacons inRegion:(CLBeaconRegion *)region
