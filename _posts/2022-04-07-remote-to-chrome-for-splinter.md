---
topmost: false #置顶
layout: post
title: splinter通过远程调试的方式链接chrome
categories: [Python, 爬虫]
author: CHY
description: splinter通过远程调试的方式链接chrome
keywords: 陈宏业, CHY, 一切随猿, 教程, 网站, splinter, Chrome, 爬虫, 反爬虫, subprocess, remote-debugging-port, 调试Chrome, selenium, Python, Python3.8.5, PyCharm, chromedriver
---

### 一、情景导入
如果通过splinter直接启动chrome浏览器，`window.navigator.webdriver`会被标记为True，本次启动的浏览器是通过`webdriver`启动的浏览器，很多大厂的网站就会通过这个特征码来甄别并防范爬虫，但是如果需要降低被甄别的概率，那就得通过远程的方式进行chrome的操作了

### 二、关键词
反扒虫、splinter、chrome、远程调试、webdriver

### 三、分析思路
总所周知，我们如果需要访问一个网站，就得先启动浏览器并输入url进行访问，然而我们人工输入的网址，只要不是刷新过快的，基本都不会被检测，那按照上面的情景导入了，我们可以先用代码模拟人工启动浏览器，然后通过splinter的options来添加远程调试地址，然后再通过visit去访问目标网址即可

### 四、工具环境
+ Windows 10
+ Python 3.8.5
+ PyCharm 2021.1.3
+ Chrome 99
+ chromedriver.exe
+ splinter 0.17.0
+ selenium 4.1.2

### 五、实现步骤
1. 通过`subprocess`启动`chrome`,假如浏览器的安装路径为`C:\Program Files\Google\Chrome`，执行以下代码：

    ```python
    import subprocess
    subprocess.Popen('C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe --remote-debugging-port=9222 --user-data-dir="C:\\split\\ChromeProfile"')
    ```
    * `--remote-debugging-port`，这是指定`chrome`的调试端口，需要与下文种的`debuggerAddress`相互呼应

    * `--user-data-dir`，这个参数指定一个独立的目录存放产生的用户数据，在连接时也要设置，否则会失效

1. 通过`chromedriver`连接已经打开的浏览器，执行以下代码：

    ```python
    from selenium.webdriver.chrome.options import Options
    from splinter.driver.webdriver.chrome import WebDriver as ChromeWebDriver

    chrome_options = Options()
    chrome_options.add_experimental_option("debuggerAddress", "127.0.0.1:9222")
    browser: Optional[ChromeWebDriver] = Browser('chrome', headless=False, incognito=incognito,options=chrome_options)
    browser.visit('https://www.baidu.com')
    ```
1. 执行自己的逻辑代码

### 六、实操代码
```python
import subprocess

from selenium.webdriver.chrome.options import Options
from splinter.driver.webdriver.chrome import WebDriver as ChromeWebDriver

def init_browser():
    subprocess.Popen('C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe --remote-debugging-port=9222 --user-data-dir="C:\\split\\ChromeProfile"')
    chrome_options = Options()
    chrome_options.add_experimental_option("debuggerAddress", "127.0.0.1:9222")
    browser: Optional[ChromeWebDriver] = Browser('chrome', headless=False, incognito=incognito,options=chrome_options)
    return browser

if __name__ == '__main__':
    browser = init_browser()
    browser.visit('https://www.baidu.com')
```

### 七、归纳总结
与通过驱动直接启动`Chrome`的方式相比，远程启动的方式就多了两步：
    
  * 通过代码手动启动加入了调试端口的浏览器；

  * 对初始化`Chrome`时加入`debuggerAddress`配置；

  * 剩下的使用步骤与原来一致。

### 八、注意事项
每天学会一点爬虫技术，日子可是越来越有判头了

切勿用于非法用途