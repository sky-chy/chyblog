---
topmost: false #置顶
layout: post
title: splinter通过远程调试的方式链接chrome
categories: [Python, 爬虫]
author: CHY
description: splinter通过远程调试的方式链接chrome
keywords:陈宏业, CHY, 一切随猿, 教程, 网站,
---

### 一、情景导入
如果通过splinter直接启动chrome浏览器，`window.navigator.webdriver`会被标记为True，本次启动的浏览器是通过`webdriver`启动的浏览器，很多大厂的网站就会通过这个特征码来甄别并防范爬虫，但是如果需要降低被甄别的概率，那就得通过远程的方式进行chrome的操作了

### 二、关键词
反扒虫、splinter、chrome、远程调试、webdriver

### 三、分析思路
总所周知，我们如果需要访问一个网站，就得先启动浏览器并输入url进行访问，然而我们人工输入的网址，只要不是刷新过快的，基本都不会被检测，那按照上面的情景导入了，我们可以先用代码启动浏览器，然后通过splinter的options来添加远程调试地址，然后再通过visit去访问目标网址即可

### 四、工具环境
+ Windows 10
+ Python 3.8.5
+ PyCharm 2021.1.3
+ Chrome 99
+ chromedriver.exe
+ splinter 0.17.0
+ selenium 4.1.2

### 五、实现步骤
1. 启动`chrome`,假如浏览器的安装路径为`C:\Program Files\Google\Chrome\Application`，执行以下代码：

    ```python
    import subprocess
    subprocess.Popen('C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe --remote-debugging-port=8999 --user-data-dir=c:"C:\\splinter\\ChromeProfile"')
    ```

1. 通过`chromedriver`链接已经打开的浏览器，执行以下代码：

    ```python
    from selenium.webdriver.chrome.options import Options
    chrome_options = Options()
    chrome_options.add_experimental_option('debuggerAddress','')
    ```

### 六、实操代码
无

### 七、归纳总结
无

### 八、注意事项
无

### 九、相关资源
无
