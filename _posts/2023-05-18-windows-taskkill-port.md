---
topmost: false #置顶
layout: post
title: Windows10下查杀占用端口的程序
categories: [Windows端口查杀]
author: CHY
description: Windows10下查杀占用端口的程序
keywords: 陈宏业, CHY, 一切随猿, 教程, 网站, 进程查杀, 端口查杀, netstat -ano, tasklist|findstr, taskkill
---

### 一、相关介绍
近日在运行vue程序的时候，发现8080端口被占用，然后自动使用8081端口，但是Chrome浏览器记录的账密跟完整的域名有关系，所需需要查询占用进程的端口

### 二、工具环境
+ Windows 10

### 三、实操代码
1. 按下<kbd>Win+R</kbd>，在弹出来的运行窗口里面输入`CMD`并回车，打开命令提示符程序
1. 执行`netstat -ano|findstr 8080`，然后在输出的信息里面找打占用端口的程序，并记录最后一列的数字(pid)，
1. 执行`tasklist|findstr pid`，会查到对应的程序，并记录程序全程，例如此时找到一个名为`node.exe`的程序占用了端口
1. 执行`taskkill /f /t /im node.exe`即可查杀占用进程的程序
1. 再次重启服务即可

### 四、归纳总结
无

### 五、注意事项
无

### 六、相关资源
无
