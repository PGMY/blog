---
layout: post
title: "Push notification snippet"
date: 2015-12-14 12:26:45 +0900
comments: true
category:
tags: []
keywords:
published: true
---
ただのめもだよー。

```
    if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 8.0)
    {
        [[UIApplication sharedApplication] registerUserNotificationSettings:
         [UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeSound | UIUserNotificationTypeAlert | UIUserNotificationTypeBadge) categories:nil]];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    } else {
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes:(UIUserNotificationTypeBadge |UIUserNotificationTypeSound | UIUserNotificationTypeAlert)];
    }
```

認証まわりがまとまったライブラリもある。
「PermissionScope」便利だなー。

<div class="github-widget" data-repo="nickoneill/PermissionScope"></div>
