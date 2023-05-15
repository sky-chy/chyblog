---
topmost: false #置顶
layout: post
title: Ubuntu22.04.2 LTS使用Docker部署Supervisord+Gunicorn+Nginx+Python3.11+Fastapi
categories: [Docker, Supervisord, Gunicorn, Nginx, Fastapi, Ubuntu]
author: CHY
description: Ubuntu22.04.2 LTS使用Docker部署Supervisord+Gunicorn+Nginx+Python3.11+Fastapi
keywords: 陈宏业, CHY, 一切随猿, 教程, 网站, Docker, Supervisord, Gunicorn, Nginx, Fastapi, Python3.11, Python3, Ubuntu22.04.2
---

### 一、情景导入
无

### 二、关键词
* Ubuntu22.04.2
* Docker容器
* Supervisord
* Gunicorn
* Nginx
* 部署Fastapi 

### 三、分析思路
无

### 四、工具环境
+ MacOS 10.15.7
+ Ubuntu 22.04.2 LTS
+ Docker 23.0.6
+ Python 3.11.x
+ Fastapi 0.95.0

### 五、实现步骤
1. 安装Docker:
    + 执行`sudo apt update`，更新软件包列表
    + 执行`sudo apt install apt-transport-https ca-certificates curl software-properties-common`，安装依赖包以允许使用 HTTPS 通过 apt 访问 Docker 仓库
    + 执行`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`，添加 Docker 官方 GPG 密钥
    + 执行`echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`，添加 Docker 软件源
    + 执行`sudo apt update`，更新软件包列表
    + 执行`sudo apt install docker-ce docker-ce-cli containerd.io`，安装 Docker
    + 执行`sudo service docker start`，启动 Docker 服务
    + 执行`sudo docker run hello-world`，如果一切顺利，你将看到一个 Hello World 的提示消息，表示 Docker 已经成功安装并运行

    + `sudo service docker status`，可以查看docker的运行状态

1. 创建Docker容器：
    + 

### 六、实操代码
无

### 七、归纳总结
无

### 八、注意事项
无

### 九、相关资源
无
