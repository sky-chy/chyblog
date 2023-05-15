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

1. 构建Docker容器： 

1. 运行Docker:

### 六、实操代码
1. 安装Docker:
    + 执行`sudo apt update`，更新软件包列表
    + 执行`sudo apt install apt-transport-https ca-certificates curl software-properties-common`，安装依赖包以允许使用 HTTPS 通过 apt 访问 Docker 仓库
    + 执行`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`，添加 Docker 官方 GPG 密钥
    + 执行`echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`，添加 Docker 软件源
    + 执行`sudo apt update`，更新软件包列表
    + 执行`sudo apt install docker-ce docker-ce-cli containerd.io`，安装 Docker
    + 执行`sudo service docker start`，启动 Docker 服务
    + 执行`sudo docker run hello-world`，如果一切顺利，你将看到一个 Hello World 的提示消息，表示 Docker 已经成功安装并运行

1. 构建Docker容器：
    * 编写Gunicorn配置文件：
        在项目的config文件夹下新建一个名称为`gconfig.py`的文件，并填入如下代码：
        ```python
        import multiprocessing

        proc_name = 'gunicorn_education_project'  # 进程名
        timeout = 120  # 设置超时时间120s，默认为30s。按自己的需求进行设置timeout = 120
        debug = True
        # 修改代码时自动重启
        reload = True
        #
        # reload_engine = 'inotify'
        # reload_extra_files = []
        # //绑定与Nginx通信的端口
        # bind = '0.0.0.0:8000'
        bind = 'unix:/tmp/gunicorn.sock'
        # pidfile = './log/gunicorn.pid'

        # workers = 4  # 进程数
        workers = multiprocessing.cpu_count() * 2 + 1  # 进程数
        threads = 3  # 指定每个进程开启的线程数

        worker_class = 'uvicorn.workers.UvicornWorker'  # 默认为阻塞模式，最好选择gevent模式,默认的是sync模式
        # 日志级别
        # debug:调试级别，记录的信息最多；
        # info:普通级别；
        # warning:警告消息；
        # error:错误消息；
        # critical:严重错误消息；
        loglevel = 'debug'
        # 访问日志路径
        accesslog = '/var/log/gunicorn/access.log'
        # 错误日志路径
        errorlog = '/var/log/gunicorn/error.log'
        # 设置gunicorn访问日志格式，错误日志无法设置
        access_log_format = '%(t)s %(p)s %(h)s "%(r)s" %(s)s %(L)s %(b)s %(f)s" "%(a)s"'

        # 执行命令
        # gunicorn -c gconfig.py main:app
        # gunicorn -c gconfig.py main:app -k uvicorn.workers.UvicornWorker
        ```
    
    * 编写Nginx配置文件：
        在项目的config文件夹下新建一个名称为`nginx.conf`的文件，并填入如下代码：
        ```conf
        worker_processes 1;

        events {
            worker_connections 1024;
        }

        http {
            upstream xxx {
                server unix:/tmp/gunicorn.sock fail_timeout=0;
            }

            server {
                listen 80;
                server_name 你的域名 127.0.0.1; #需要将yourdomain.com替换成证书绑定的域名。
                root /www/docker_server/xxx;
        #         rewrite ^(.*)$ https://$host$1; #将所有HTTP请求通过rewrite指令重定向到HTTPS。
        #         location / {
        #             index index.html index.htm;
        #         }

                location / {
                    proxy_set_header x-Real-IP $remote_addr;
                    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_set_header Host $http_host;
                    include proxy_params;
                    proxy_pass http://xxx;
                }
                location /coronavirus {
                    alias /www/docker_server/xxx/coronavirus/static;
                }
                location /txt {
                    alias /www/docker_server/xxx/coronavirus/txt;
                }
            }
        }

        ```
    
    * 编写Supervisor配置文件:
        在项目的config文件夹下新建一个名称为`supervisor.ini`的文件，并填入如下代码：
        ```conf
        [supervisord]
        nodaemon=true

        [program:gunicorn_renren_education]
        command=/gunicorn -c /www/docker_server/renren-education/config/gconfig.py main:app
        directory=/www/docker_server/xxx #项目目录
        user=root
        autorestart=true #设置自动重启
        startretires=3  #重启失败3次
        stdout_logfile=/var/log/supervisor/renren-education-stdout.log
        stderr_logfile=/var/log/supervisor/renren-education-stderr.log

        [program:nginx_renren_education]
        command=nginx -c /www/docker_server/xxx/config/nginx.conf
        ```

    * 编写Docker配置文件：
        在项目的根目录下新建一个名称为`Dockerfile`的文件，并填入如下代码：
        ```conf
        # 使用 Python 3.11 作为基础镜像
        FROM python:3.11

        # 设置工作目录
        WORKDIR /www/docker_server/xxx

        # 复制项目文件到容器中
        COPY . /www/docker_server/xxx
        
        # 安装supervisor
        RUN apt-get update && apt-get install -y supervisor

        # 安装项目依赖
        RUN pip install --no-cache-dir -r requirements.txt

        # 暴露端口
        EXPOSE 80
        EXPOSE 443

        # 启动 Supervisord
        CMD ["supervisord", "-c", "config/supervisord.ini"]
        ```

    * 构建容器：
        + 执行`cd /www/server/xxx`命令，切换到项目路径
        + 执行`sudo docker build -t xxx .`命令，开始构建容器
            `-t xxx`：指定了要构建的镜像的名称和标签。-t 是标签的缩写，xxx 是镜像的名称。可以根据需要修改名称和标签。
            `.`：表示当前目录，它告诉 Docker 在当前目录中查找 Dockerfile 文件并使用它来构建镜像。

    * 将 nginx.conf 文件复制到 Docker 容器中：
        + 执行`sudo docker cp config/nginx.conf <容器ID或容器名称>:/etc/nginx/nginx.conf`命令
            `config/nginx.conf`，表示的是项目中的nginx配置文件
            `<容器ID或容器名称>:/etc/nginx/nginx.conf`，表示的是容器内的目标路径，表示文件将被复制到容器中的`/etc/nginx` 目录下，并被命名为`nginx.conf`
 
1. 运行Docker:
    * 执行`sudo docker run -d -p 80:80 --name aaa xxx`命令，这将在后台运行一个名为 xxx 的 Docker 容器，并将主机的 80 端口映射到容器的 80 端口
        + `-d`：是一个选项，表示在后台模式下运行容器，docker
        + `-p 80:80`：可以将主机的 80 端口与容器的 80 端口进行映射，Dockerfile文件中的`EXPOSE`命令只是声明容器内部的端口，但并不会自动进行主机端口的映射
        + `aaa`:为容器指定一个可识别的名称
        + `xxx`：要运行的 Docker 镜像的名称。通过指定镜像名称，Docker 将在该镜像的基础上创建并运行一个容器，这个xxx与`sudo docker build -t xxx .`中的```xxx```是相关的，通过构建镜像并为其指定名称后，可以使用该名称来运行该镜像创建的容器。

### 七、归纳总结
无

### 八、注意事项
如果项目文件有更新，但是不更新容器的配置文件，可以执行`sudo docker run -d -p 80:80 --name 容器名称 -v /www/server/xxx:/www/docker_server/xxx 容器镜像名称`命令进行挂载更新，`-v /www/server/xxx:/www/docker_server/xxx`部分是用于将本地的 /www/server/xxx 目录挂载到容器内的 /www/docker_server/xxx 目录，以实现主机和容器之间的文件共享，这样，新的项目文件将被复制到容器中，并且容器将在更新后重新启动。


### 九、相关资源
* `sudo docker images`， 这将列出所有已构建的 Docker 镜像
* `sudo docker ps`，列出当前正在运行的容器
* `sudo docker ps -a`，查看所有容器的状态
* `sudo docker start <容器ID或容器名称>`， 启动该容器
* `sudo docker stop <容器ID或容器名称>`，停止该容器
* `sudo docker rm <容器ID或容器名称>`，删除该容器
* `sudo docker restart <容器ID或容器名称>`，重新启动容器
* `sudo docker rmi <容器名称>`，删除容器镜像
* `sudo docker logs <容器名称>`，可以查看容器的日志
* `sudo service docker status`，可以查看docker的运行状态
