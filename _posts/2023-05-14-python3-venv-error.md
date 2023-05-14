---
topmost: false #置顶
layout: post
title: Python3创建自带虚拟环境报错
categories: [Python3, Python, venv]
author: CHY
description: python3 -m venv xxxx时，报Error: Command '['E:\\Code\\Python\\Git\\test1\\aaa\\Scripts\\python.exe', '-Im', 'ensurepip', '--upgrade', '--default-pip']' returned non-zero exit status 1.)
keywords: 陈宏业, CHY, 一切随猿, 教程, 网站, Python, Python3, 虚拟环境, venv, Python -m venv, Python3 -m venv
---

### 一、情景导入
新开发了一个fastapi的后端，部署Ubuntu服务器的时候需要创建虚拟环境，但是在执行`python3 -m venv xxxx`创建虚拟环境的的时候报rror: Command `'Error: Command '['/www/venv/xxx/bin/python3.11', '-m', 'ensurepip', '--upgrade', '--default-pip']' returned non-zero exit status 1.`，然而该路径下却存在这个文件夹以及部分内容，如何解决这个问题呢？

### 二、关键词
Python3.11, 创建自带虚拟环境

### 三、分析思路
根据度娘的反馈得知，其实这主要是因为Python安装没有包含ensurepip模块，或者你的网络连接有问题导致pip无法下载依赖包。

### 四、工具环境
+ MacOS 10.15.7
+ Ubuntu 18.04
+ Python 3.11
+ 阿里云ECS

### 五、实现步骤
1. 删除已创建的虚拟环境
1. 创建不带pip的虚拟环境
1. 安装虚拟环境中的pip

### 六、实操代码
1. 执行命令 `rm -r /www/venv/xxx`删除这个文件夹
1. 执行命令 `python3 -m venv /www/venv/xxx --without-pip`重新创建虚拟环境

如果执行上述两个步骤还是无法装上
1. 执行命令 `source /www/venv/xxx/bin/active` 激活虚拟环境
1. 执行命令 `python -m pip uninstall pip` 卸载新环境中的旧pip
1. 然后如果你使用的python版本低于3.4甚至是py2，那么你需要使用get-pip.py脚本文件(https://bootstrap.pypa.io/get-pip.py，进去这个网站，然后根据你自己python的版本选择对应的get-pip.py)来手动安装 pip，如果选择错版本可能会报错： `ERROR: This script does not work on Python 3.6 The minimum supported Python version is 3.7. Please use https://bootstrap.pypa.io/pip/3.6/get-pip.py instead.`

### 七、归纳总结
无

### 八、注意事项
无

### 九、相关资源
1. https://bootstrap.pypa.io/get-pip.py
