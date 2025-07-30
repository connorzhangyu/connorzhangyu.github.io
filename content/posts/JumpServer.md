---
title: JumpServer
categories:
  - Linux
tags:
  - Linux
  - JumpServer
cover: https://image.runtimes.cc/202404051529843.jpeg
abbrlink: 44255
date: 2023-10-25 09:30:33
updated: 2024-06-15 16:05:06
---

# 笔记
## 安装JumpServer

[官网一键安装](https://community.fit2cloud.com/#/products/jumpserver/getstarted)

## 修改配置远程访问的端口

```shell
# 进入到jp的配置文件目录
cd /opt/jumpserver/config
# 修改配置文件
vim config.txt
# 修改配置,对外提供服务端口, 如果与现有服务冲突请自行修改
HTTP_PORT=8083  # 修改这个端口

# 重新启动
cd /opt/jumpserver-installer-v3.6.3
./jmsctl.sh restart
```

## 修改系统管理员密码
```shell
# 进入Docker容器
$ docker exec -it jms_core /bin/bash

# 进入根目录,如果不是Docker,进入cd /opt/jumpserver/apps
$ cd apps/

$ python manage.py changepassword  admin
# 输入新的密码
$ password
```

## Nginx反向代理
[Nginx反向代理](../9355/#代理JumpServer)

## 配置SSL

```shell
# 先停止服务
cd /opt/jumpserver-installer-v3.6.3
./jmsctl.sh stop

# 把证书移动到这个目录
/opt/jumpserver/config/nginx/cert

# 修改配置文件,域名和ssl证书的文职
/opt/jumpserver/config/config.txt

# 启动服务
cd /opt/jumpserver-installer-v3.6.3
./jmsctl.sh start
```

## 配置SSH超时时间

默认的时间好像半个小时不操作了,就会自动断开,改成最大值

![image-20240615160327165](https://image.runtimes.cc/202406151603157.png)
