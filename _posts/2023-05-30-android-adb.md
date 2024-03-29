---
topmost: false #置顶
layout: post
title: Android的常见ADB命令
categories: [Android, ADB]
author: CHY
description: Android的常见ADB命令
keywords: 一切随猿, 技术网站, Android教程, Android ADB, ADB使用, ADB Shell, Android 网络调试
---

### 一、前言
Android的常见ADB命令，以及相关命令的简单使用

### 二、正文

1. ADB通过网络连接手机：
   * 先把手机通过USB接入电脑，如果通过下属命令可以直接连接到手机的，则不需要执行这一步;
   * 执行```adb kill-server```命令，将ADB进程杀掉；
   * 执行```adb tcpip 5555```命令，将ADB通讯端口设为5555，该端口一般为Android系统默认调试的通讯端口；
   * 把连接Android手机的USB数据线拔出；
   * 执行```adb connect xxx.xxx.x.xxx```命令，将通过adb连接Android手机，xxx.xxx.x.xxx为Android手机的IP地址

1. 执行```adb devices```命令，可以查看已经连接的设备；

1. 执行```adb shell am start -a android.intent.action.VIEW -d 'https://wwww.baidu.com'```命令，可以打开系统的浏览器并访问```https://wwww.baidu.com```

1. 执行```adb shell settings delete global http_proxy```或者```adb shell settings delete global global_http_proxy_host```可以取消代理


### 三、注意事项
如果有多台设备连接，需要通过ADB命令进行调试，则需要加入```-s ip:port```来指定连接哪台设备

### 四、相关资源
无
