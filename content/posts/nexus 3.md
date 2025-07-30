---
title: nexus 3
id: d7627a82-804d-4461-8a61-47f0a7d329a3
date: 2025-01-04 03:56:02
auther: connor
cover: null
excerpt: title nexus3 tags 仓库 categories 记录 cover https//image.runtimes.cc/202409151814537.png abbrlink 55592 date 2024-03-12 185243 updated 2024-09-
permalink: /archives/08937d47-b9d9-4827-9a97-bcf8f198e43e
---

---
title: nexus3
tags:
  - 仓库
categories:
  - 记录
cover: https://image.runtimes.cc/202409151814537.png
abbrlink: 55592
date: 2024-03-12 18:52:43
updated: 2024-09-15 18:10:26
---

# Docker仓库

| 类型  | 端口 | 仓库名字        |
| ----- | ---- | --------------- |
| Host  | 9083 | My-docker-host  |
| proxy |      | My-docker-proxy |
| group | 9082 | My-docker-group |

**创建一个存储目录**

名字随便起,没有太多影响

![image-20240917104642184](../../../../../../Library/Application Support/typora-user-images/image-20240917104642184.png)

创建docker host仓库

![image-20240917104411323](../../../../../../Library/Application Support/typora-user-images/image-20240917104411323.png)

![image-20240917104515460](../../../../../../Library/Application Support/typora-user-images/image-20240917104515460.png)

![image-20240917104240181](../../../../../../Library/Application Support/typora-user-images/image-20240917104240181.png)



创建proxy仓库

![image-20240917113108378](../../../../../../Library/Application Support/typora-user-images/image-20240917113108378.png)

![image-20240917113128237](../../../../../../Library/Application Support/typora-user-images/image-20240917113128237.png)

group

![image-20240917113647416](../../../../../../Library/Application Support/typora-user-images/image-20240917113647416.png)

docker客户端配置

```shell
# vim /etc/docker/daemon.json
{
  "insecure-registries": ["54.255.206.44:9082", "54.255.206.44:9083"]
}

# 需要重启
systemctl daemon-reload
systemctl restart docker
```

登录

登录之后凭证保存在`/.docker/config.json`或者是在,部署的时候遇到一个问题,就是jenkins打包之后docker login可以,但是push的时候就提示没有授权,暂时的解决办法是,宿主机docker login下,保存凭证,然后jenkins容器挂载`/root/.docker/config.json`这个文件,jenkins打包就可以不用登录,直接推送

```shell
root@anthony:~# docker login http://54.255.206.44:9083
Username: admin
Password:
Error response from daemon: login attempt to http://54.255.206.44:9083/v2/ failed with status: 401 Unauthorized
# ----------------------------------------------------------------------------
root@anthony:~# docker login http://54.255.206.44:9082
Username: admin
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

```shell
# 镜像操作
# pull镜像
docker pull nginx:latest

# 给镜像打tag
docker tag nginx:latest 54.255.206.44:9082/my-docker-host/nginx:latest

# 推送镜像
docker push 54.255.206.44:9082/my-docker-host/nginx:latest
```