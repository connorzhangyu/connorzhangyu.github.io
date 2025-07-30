---
title: Harbor
categories:
  - Docker
tags:
  - Docker
  - Harbor
  - 构建
  - 自动化
cover: https://image.runtimes.cc/202404051528484.png
abbrlink: 13221
date: 2022-11-19 18:25:00
updated: 2022-11-21 18:49:00
---

## 下载离线安装包

![](https://image.runtimes.cc/202404051422363.png)

```bash
# 解压
tzr -zxvf harbo.xxxxx.tgz

# cp 下模板文件
cp ./harbor.yml.tmpl ./harbor.yml
```

## 修改配置文件

```bash
sudo vim harbor.yml
```

```yaml
# 有域名就弄域名,这里是在内网就用的ip
hostname: 10.0.2.4

# http related config
http:
  # port for http, default is 80. If https enabled, this port will redirect to https port
  port: 80

# 内网不需要https,https和证书就注释了
# https related config
#https:
  # https port for harbor, default is 443
  # port: 443
  # The path of cert and key files for nginx
  # certificate: /your/certificate/path
  # private_key: /your/private/key/path

# Uncomment external_url if you want to enable external proxy
# And when it enabled the hostname will no longer used
# external_url: https://reg.mydomain.com:8433

# The initial password of Harbor admin
# It only works in first time to install harbor
# Remember Change the admin password from UI after launching Harbor.
harbor_admin_password: 123456

# Harbor DB configuration
database:
  # The password for the root user of Harbor DB. Change this before any production use.
  password: root123
  # The maximum number of connections in the idle connection pool. If it <=0, no idle connections are retained.
  max_idle_conns: 50
  # The maximum number of open connections to the database. If it <= 0, then there is no limit on the number of open connections.
  # Note: the default number of connections is 100 for postgres.
  max_open_conns: 100

# The default data volume
# 这里要配置好目录
data_volume: /home/yunxian/harbor/data
```

## 运行

```yaml
# 每次修改完配置文件后都需要运行
./prepare

# 运行
./install.sh
```

## 访问

访问:[`http://10.0.2.4/`](http://10.0.2.4/)

账号密码:admin / 123456

# 解决客户端的https问题

```bash
[root@client ~]# docker login 10.19.46.15
Username: admin
Password: 
Error response from daemon: Get https://10.19.46.15/v2/: dial tcp 10.19.46.15:443: connect: connection refused
```

这是因为docker1.3.2版本开始默认docker registry使用的是https，我们设置Harbor默认http方式，所以当执行用`docker login、pull、push`等命令操作非https的`docker regsitry`的时就会报错。

解决办法

`vim docker-compose.yml`

![](https://image.runtimes.cc/202404051422110.png)

编辑客户端

```bash
vim /etc/docker/daemon.json

{
    "insecure-registries": [
        "10.19.46.15(harbor的IP)"
    ]
}

# 重启docker
systemctl daemon-reload
systemctl restart docker
```

再次登录

```bash
[root@client ~]# docker login 10.19.46.15   
Username: admin
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
Login Succeeded
```

# 技巧

pull 的命令

![](https://image.runtimes.cc/202404051422738.png)

推送和打tag的命令

![](https://image.runtimes.cc/202404051423924.png)