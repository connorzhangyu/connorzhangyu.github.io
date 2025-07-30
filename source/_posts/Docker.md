---
title: Docker
tags:
  - Docker
  - Jenkins
categories:
  - Docker
cover: https://image.runtimes.cc/202404051526227.webp
abbrlink: 40991
date: 2022-11-19 18:25:00
updated: 2024-09-15 18:10:21
---

# docker安装
{% tabs docker %}
<!-- tab Centos-->
```bash
# 卸载旧版本
sudo yum remove docker docker-client  docker-client-latest  docker-common  docker-latest  docker-latest-logrotate  docker-logrotate  docker-selinux  docker-engine-selinux  docker-engine

# 安装
sudo yum install -y yum-utils  device-mapper-persistent-data  lvm2

# 添加yum源
sudo yum-config-manager --add-repo  https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo

# 安装
yum -y install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.6-3.3.fc30.x86_64.rpm
yum -y install docker-ce

启动
systemctl enable docker
systemctl start docker
```
<!-- endtab -->

<!-- tab Ubuntu-->
```bash
# 用于系统是干净的,如果重新装,需要吧原来的docker卸载掉
sudo apt install docker.io
```
<!-- endtab -->
{% endtabs %}

## 开启远程访问

```bash
# 编辑
vim /lib/systemd/system/docker.service

# 默认是这样的
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock 
# 修改成
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock

# 重启
systemctl daemon-reload
systemctl restart docker

# 测试
curl http://localhost:2375/version
```

## 查看远端仓库的标签

创建一个`dockertags.sh`脚本

```bash
#!/bin/bash

function usage() {
    cat << HELP

dockertags  --  list all tags for a Docker image on a remote registry.

EXAMPLE:
    - list all tags for ubuntu:
       dockertags ubuntu

    - list all php tags containing apache:
       dockertags php apache

HELP
}

if [ $# -lt 1 ]; then
    usage
    exit
fi

image="$1"
tags=`wget -q https://registry.hub.docker.com/v1/repositories/${image}/tags -O -  | sed -e 's/[][]//g' -e 's/"//g' -e 's/ //g' | tr '}' '\n'  | awk -F: '{print $3}'`

if [ -n "$2" ]
then
    tags=` echo "${tags}" | grep "$2" `
fi

echo "${tags}"
```

```bash
# 给权限
chmod +x dockertags.sh

# 使用
./dockertags.sh mysql
```

## WARNING: IPv4 forwarding is disabled. Networking will not work

是没有开启转发,[docker](https://so.csdn.net/so/search?q=docker&spm=1001.2101.3001.7020)网桥配置完后，需要开启转发，不然容器启动后，就会没有网络

修改配置文件：

```shell
# vim /etc/sysctl.conf
net.ipv4.ip_forward=1    #添加此行配置

# 重启network和docker服务
systemctl restart network && systemctl restart docker

# 查看是否修改成功，如果返回为“net.ipv4.ip_forward = 1”则表示修改成功
sysctl net.ipv4.ip_forward
```

## 常用命令

```shell
# 查看镜像源,最下面能看到
docker info

# 查看镜像/容器/数据卷所占的空间
docker system df xxx
```

# 镜像

**查看镜像**

```shell
# 列出本地主机上的镜像
docker images
```

- REPOSITORY：表示镜像的仓库源
- TAG：镜像的标签
- IMAGE ID：镜像ID
- CREATED：镜像创建时间
- SIZE：镜像大小

**下载镜像**

```shell
# 查找镜像
docker search ubuntu:13.10

# 只是直接下载
docker pull ubuntu:13.10
```

**删除镜像**

```shell
# 删除指定镜像
docker image rm <id>

# 删除名字是none的镜像
docker rmi $(docker images | grep "none" | awk '{print $3}')

# 删除全部镜像
docker rmi -f $(docker image -qa)
```

# 容器

**运行容器**

```shell
docker run -p 10001:10001 -t springboot/eureka-item
```

```shell
docker run -it --rm ubuntu:16.04 bash
```

- docker run 运行容器的命令
- -i 以交互模式运行,通常与-t一起使用
- -t 为容器重新分配一个伪输入终端,通常与-i一起使用
- bash 进入交互式终端,通常使用/bin/bash
- -p 指定端口
- -P 随机分配端口
- -d 后台运行容器并返回容器Id,即启动守护式容器

**查看容器**

```shell
# 查看正在运行的容器
docker ps 

# 查看所有容器
docker ps -a

# 列出所有的容器 ID
docker ps -aq
```

**启动容器**

```shell
 #启动已终止容器
 docker container start 05909cd09bf9
```

**重启容器**

```shell
docker restart id
```

**停止容器**

```shell
# 停止容器
docker stop myredis

# 强制停止容器
docker kill myredis

# 停止所有的容器
docker stop $(docker ps -aq)

# 删除所有的容器
docker rm $(docker ps -aq)

# 强制删除,在运行的容器也会删除
docker rm -f myredis
```

**容器日志**

```shell
docker logs xxx 
```

## 进入容器

```shell
# 不会启动新的进程,用exit退出,会导致容器的停止
docker attach 5cc239848ce5

# 打开新的终端,启用新的进程,用exit退出,不会导致容器的停止
docker exec -it 5cc239848ce5 /bin/bash
```

**进入容器修改时区,需要重启容器**

```shell
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
echo "Asia/Shanghai" > /etc/timezone
```

## 拷贝文件

```shell
# 将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下。
docker cp /www/runoob 96f7f14e99ab:/www/

# 将主机/www/runoob目录拷贝到容器96f7f14e99ab中，目录重命名为www
docker cp /www/runoob 96f7f14e99ab:/www

# 将容器96f7f14e99ab的/www目录拷贝到主机的/tmp目录中
docker cp  96f7f14e99ab:/www /tmp/
```

## 导出/入容器

第一种方式:

```shell
# 如果要导出本地某个容器，可以使用 docker export 命令。
docker export 1e560fca3906 > ubuntu.tar

# docker import 从容器快照文件中再导入为镜像，
# 以下实例将快照文件 ubuntu.tar 导入到镜像 test/ubuntu:v1:
# 格式: cat 文件名 | docker import - xxx(镜像用户,自定义)/xxx(镜像名,自定义):xxx(版本号自定义)
cat docker/ubuntu.tar | docker import - test/ubuntu:v1
```

第二种方式:

```shell
# 镜像转文件
docker save -o demo.tar(自定义) 镜像名:版本号

# 文件转镜像
docker load -i demo.tar
```

## Commit

```shell
docker commit -a "runoob.com" -m "my apache" a404c6c174a2  mymysql:v1 
```

## 过时:容器不能上网

```bash
vim /etc/sysctl.conf
增加：net.ipv4.ip_forward=1
重启服务：systemctl restart network
查看属性是否修改成功：sysctl net.ipv4.ip_forward
```

## 过时:停止和查看容器

```bash
#查看所有的容器(已经存在的容器,已经停止的)
docker container ls -a

#停止容器
docker container stop 05909cd09bf9
```


## 过时:Jenkins容器脚本
```bash

APP_NAME=springboot/lottery-admin

echo "当前容器列表"
docker ps -a | grep $APP_NAME
echo "star service success!"

count = ’docker ps -a |grep $APP_NAME |wc -l‘

if [$count -ge 1];then
docker stop $APP_NAME
docker rm $APP_NAME
fi
```

```bash
appName="simons-cloud-eureka"
word="1"
echo "$word"
word=`docker ps -a -q --no-trunc --filter name=^/"$appName"$`
echo "$word"
if [ -z "$word" ];
then
echo "当前不存在该容器，直接进行启动该操作-------------------------------------"
elif [ -n "$word" ];
then
echo "当前已存在容器，停止并移除该容器-------------------------------------"
/usr/bin/docker stop "$word"
/usr/bin/docker rm "$word"
elif [ "$word" == "1" ];
then
echo "查询的信息有误，执行默认操作-------------------------------------"
/usr/bin/docker stop "$word"
/usr/bin/docker rm "$word"
fi

docker run -p 8761:8761 -d --name "$appName" "$appName":latest
```

# 挂载目录

```shell
docker run -it --privileged=true -v /tmp/host_data:/tmp/docker_data --name=u1 ubuntu
# --privileged=true 用来扩容权限用的,最好是加上
# -v 宿主目录:容器目录,运行容器的时候,会自动创建目录

# 查看挂载情况
"Mounts": [
    {
        "Type": "bind",
        "Source": "/tmp/host_data",
        "Destination": "/tmp/docker_data",
        "Mode": "",
        "RW": true,
        "Propagation": "rprivate"
    }
]

# 继承挂载
# 再启动一个容器,起名叫u2,u2的挂载目录,跟u1的一样,就继承u1的挂载目录
# 特点是,就算u1停了,也不会影响这个
docker run -it --privileged=true --volumes-fron u1 --name=u2 ubuntu
```



# Dcokerfile

## Expose

暴露端口

```bash
# GET_IMAGE
FROM 192.168.0.216:5000/centos
 
# MAINTAINER_INFO
MAINTAINER hongxue hongxue@showjoy.com
 
RUN yum -y install vim
RUN yum -y install net-tools
RUN yum -y install openssh-server
RUN yum -y install wget curl
 
# PORT
EXPOSE 8080
EXPOSE 22
EXPOSE 8009
EXPOSE 8005
EXPOSE 8443
```

```bash
docker run -d -it -P --name port_list_container port_list
```

- 要使用-P ,绑定的宿主端口,会是随机的
- 所以Dockerfile的EXPOSE的主要功能,也只是给运维人员看看的



# 网络

```shell
# 查看Docker网络,默认的3大网络模式
root@anthony:~# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
a4154cc357c5   bridge    bridge    local
6625ad672ae9   host      host      local
497caf91f24e   none      null      local

# 创建一个网络
docker network create xxxx

# 删除一个网络
docker network rm xxxx

# 查看网络
docker network inspect xxx 
root@anthony:~# docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "a4154cc357c50d0ac961d8235101d8e199119d5677b911ca84ff49962039a53a",
        "Created": "2023-10-27T00:40:28.869797225Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "a59c6dd9d8bbdbc4f760389bd85af69786e632e6bd1d346c542d6886f572872b": {
                "Name": "u1",
                "EndpointID": "e54ed02028a063b38390abac5306cba4f97ff40135b0cfab2370c950f5ae11dc",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            },
            "b5615376be36a2b28485a93fa62d10d9886c53c9f59c5fbcd00de78b0184d6b7": {
                "Name": "tomcat",
                "EndpointID": "23688ac53c0f16a8abd20bbe4e64539266645019ce95501253577cf57fc71b7a",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            # 网桥名字
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```



# docker-compose

[Docker Compose](https://www.runoob.com/docker/docker-compose.html)

## 安装

```bash
# 可以修改版本 
curl -L "https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 给权限
sudo chmod +x /usr/local/bin/docker-compose

# 超链接
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# 验证
docker-compose --version
```

## 命令

```bash
# 启动
docker-compose up

# 后台启动
docker-compose up -d
```

## 部署到私有仓库

```bash
//位置
gedit /etc/default/docker

//添加的命令
DOCKER_OPTS="–insecure-registry 172.20.100.211:5000"

//重启
service docker restart

//打tag
docker tag springboot/eureka-item 172.20.100.211:5000/anthonyfirst

//推送
docker push 172.20.100.211:5000/anthonyfirst

//获取私有仓库里的信息
curl -XGET http://172.20.100.211:5000/v2/_catalog

#客户端配置私有仓库
修改/etc/sysconfig/docker（Ubuntu下配置文件地址为：/etc/init/docker.conf），增加启动选项(已有参数的在后面追加)，之后重启docker，不添加报错，https证书问题。
OPTIONS='--insecure-registry 172.20.100.211:5000' #CentOS 7系统

#重启服务
systemctl daemon-reload
systemctl restart docker
```

# 报错信息

### 1.缺少FontConfiguration

知道是因为alpine中缺少FontConfiguration，那么就考虑安装ttf-dejavu这个软件。

```java
java.lang.NullPointerException
    at sun.awt.FontConfiguration.getVersion(FontConfiguration.java:1264)
    at sun.awt.FontConfiguration.readFontConfigFile(FontConfiguration.java:219)
    at sun.awt.FontConfiguration.init(FontConfiguration.java:107)
    at sun.awt.X11FontManager.createFontConfiguration(X11FontManager.java:774)
    at sun.font.SunFontManager$2.run(SunFontManager.java:431)
    at java.security.AccessController.doPrivileged(Native Method)
    at sun.font.SunFontManager.<init>(SunFontManager.java:376)
    at sun.awt.FcFontManager.<init>(FcFontManager.java:35)
    at sun.awt.X11FontManager.<init>(X11FontManager.java:57)
    at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
    at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
    at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
    at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
    at java.lang.Class.newInstance(Class.java:442)
    at sun.font.FontManagerFactory$1.run(FontManagerFactory.java:83)
    at java.security.AccessController.doPrivileged(Native Method)
    at sun.font.FontManagerFactory.getInstance(FontManagerFactory.java:74)
    at java.awt.Font.getFont2D(Font.java:491)
    at java.awt.Font.access$000(Font.java:224)
    at java.awt.Font$FontAccessImpl.getFont2D(Font.java:228)
    at sun.font.FontUtilities.getFont2D(FontUtilities.java:180)
    at sun.font.StandardGlyphVector.initFontData(StandardGlyphVector.java:1126)
    at sun.font.StandardGlyphVector.init(StandardGlyphVector.java:1115)
    at sun.font.StandardGlyphVector.<init>(StandardGlyphVector.java:167)
    at java.awt.Font.createGlyphVector(Font.java:2545)
    at nl.captcha.text.renderer.DefaultWordRenderer.render(Unknown Source)
    at nl.captcha.Captcha$Builder.addText(Unknown Source)
    at com.liferay.portal.captcha.simplecaptcha.SimpleCaptchaImpl.getSimpleCaptcha(SimpleCaptchaImpl.java:243)
    at com.liferay.portal.captcha.simplecaptcha.SimpleCaptchaImpl.serveImage(SimpleCaptchaImpl.java:159)
    at com.liferay.portal.captcha.CaptchaImpl.serveImage(CaptchaImpl.java:100)
    at com.liferay.portal.kernel.captcha.CaptchaUtil.serveImage(CaptchaUtil.java:78)
    at com.liferay.portal.captcha.CaptchaPortletAction.serveResource(CaptchaPortletAction.java:42)
```

原本的dockerfile

```docker
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD agent-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

要改成下面这样`RUN apk --update add fontconfig ttf-dejavu`

```docker
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD agent-0.0.1-SNAPSHOT.jar app.jar
RUN apk --update add fontconfig ttf-dejavu
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

### 2.**WARNING: HK2 service reification failed for...**

```java
java.lang.NoClassDefFoundError: javax/activation/DataSource
				at java.base/java.lang.Class.getDeclaredConstructors0(Native Method)
				at java.base/java.lang.Class.privateGetDeclaredConstructors(Class.java:3110)
				at java.base/java.lang.Class.getDeclaredConstructors(Class.java:2314)
				at org.jvnet.hk2.internal.Utilities$3.run(Utilities.java:1310)
				at org.jvnet.hk2.internal.Utilities$3.run(Utilities.java:1306)
				at java.base/java.security.AccessController.doPrivileged(Native Method)
				at org.jvnet.hk2.internal.Utilities.getAllConstructors(Utilities.java:1306)
				at org.jvnet.hk2.internal.Utilities.findProducerConstructor(Utilities.java:1249)
				at org.jvnet.hk2.internal.DefaultClassAnalyzer.getConstructor(DefaultClassAnalyzer.java:83)
				at org.glassfish.jersey.internal.inject.JerseyClassAnalyzer.getConstructor(JerseyClassAnalyzer.java:144)
				at org.jvnet.hk2.internal.Utilities.getConstructor(Utilities.java:178)
				at org.jvnet.hk2.internal.ClazzCreator.initialize(ClazzCreator.java:128)
				at org.jvnet.hk2.internal.ClazzCreator.initialize(ClazzCreator.java:179)
				at org.jvnet.hk2.internal.SystemDescriptor.internalReify(SystemDescriptor.java:723)
				at org.jvnet.hk2.internal.SystemDescriptor.reify(SystemDescriptor.java:678)
```

在pom.xml里添加

```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>dockerfile-maven-plugin</artifactId>
		// ......
    <dependencies>
        <dependency>
            <groupId>javax.activation</groupId>
            <artifactId>activation</artifactId>
            <version>1.1.1</version>
        </dependency>
    </dependencies>
</plugin>
```

### 3.不能链接daemon

服务没有启动,运行docker,会报这个错

```bash
docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?.
```

需要启动docker service

```
service docker start
```

# 安装软件

## 安装Jenkins

```shell
docker run -d \
		-u root \
		-v /var/run/docker.sock:/var/run/docker.sock \
		-v $(which docker):/usr/bin/docker \
		-p 8080:8080 \
		-p 50000:50000 \
		-v /home/jenkins:/var/jenkins_home \
		--restart=always \
		--name jenkins \
		jenkins/jenkins:lts
		
# 查看密码
# 容器里的位置
cat /var/jenkins_home/secrets/initialAdminPassword
# 宿主机的位置
cat /root/jenkins/secrets/initialAdminPassword
```

4.安装插件完之后,安装maven插件,在主机上下载maven,上传到容器中

```shell
docker cp maven-3.6.0 jenkins:/usr/local/

// 上传本机的配置文件
docker cp settings.xml jenkins:/home/
```

5.进入容器

```shell
// 普通用户的权限
docker exec -it jenkins bash

// sudo的用户权限
docker exec -it -u 0 jenkins bash
```

6.从本机拷贝到容器,是不需要用到权限的,但是在容器内,比如从/home下的文件移动到/root 就需要权限,就需要使用 -u 0

> 在Docker容器的Jenkins,构建SpringBoot 的jar包
> 再执行Shell运行的时候,连接数据库可能有坑,数据库会连不上

## 安装ElasticSearch

```
docker run -d --name es -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.3.2

docker exec -it es /bin/bash

cd config

vi elasticsearch.yml

# 加入跨域配置
http.cors.enabled: true
http.cors.allow-origin: "*"

docker restart es
```

## 安装Portainer

```bash
# 创建数据卷
docker volume create portainer_data

# 9000才是web访问的端口
docker run -d \
      -p 8000:8000 \
      -p 9000:9000 \
      -p 9443:9443 \
      --name portainer \
      --restart=always \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -v /docker/portainer:/data \
      portainer/portainer-ce:2.21.1
```

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://localhost:9000;  # 将请求转发到 Portainer 后端
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket 支持
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

## 添加docker node

[https://blog.csdn.net/bj_chengrong/article/details/90300972](https://blog.csdn.net/bj_chengrong/article/details/90300972)

## 安装Redis

```shell
# 简单的执行
docker run -itd \
     --name redis \
     --restart=always \
     -p 6379:6379 \
     redis

# 持久化执行
docker run -d \
           --name redis \
           -p 6379:6379 \
           -v /docker/redis/config/redis.conf:/usr/local/etc/redis/redis.conf \
           -v /docker/redis/data:/data \
           --restart=always \
           redis redis-server /usr/local/etc/redis/redis.conf
```

新建`redis.conf`,位置在:`/docker/redis/config/redis.conf`

```yaml
#启用 RDB 快照持久化
save 900 1
save 300 10
save 60 10000

#启用 AOF 持久化
appendonly yes
appendfsync everysec

#设置持久化目录
dir /data
```

## 安装Mysql

```shell
# 正式配置
# 在对应目录先创建my.cnf文件, 不然系统会自动创建my.cnf文件夹....
# 本地对应的目录文件夹会自动创建
docker run -itd \
           --name mysql \
           -p 3306:3306 \
           -e MYSQL_ROOT_PASSWORD=123456 \
           -v /Users/anthony/docker/mysql/config/my.cnf:/etc/mysql/conf.d/my.cnf \
           -v /Users/anthony/docker/mysql/data:/var/lib/mysql \
           --restart=always  \
           mysql
```

最简单的my.cnf配置文件

```yaml
[mysqld]
# 时区
default-time-zone=+08:00
# 字符集
character-set-server=utf8mb4
# 创建函数/存过的时候,会报安全问题,不用存过/函数,不用管
log_bin_trust_function_creators=1;
```

> 容器文档:[Mysql](https://hub.docker.com/_/mysql/)

## 安装 Nginx

```docker
# 简单的
docker run --name nginx-test \
     -p 8080:80 \
     -d \
     nginx

# 正式的
docker run -d \
           -p 443:443 \
           -p 80:80 \
           --name nginx \
           -v /home/nginx/www:/usr/share/nginx/html \
           -v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
           -v /home/nginx/logs:/var/log/nginx \
           nginx
```

nginx.conf模板

```shell
# 用户 nginx 使用的工作进程
user nginx;

# 允许的工作进程数，自动检测 CPU 核心数
worker_processes auto;

# 错误日志路径和日志级别
error_log /var/log/nginx/error.log warn;

# PID 文件路径
pid /var/run/nginx.pid;

events {
    # 单个工作进程允许的最大连接数
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # 日志格式配置
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    # 访问日志路径
    access_log /var/log/nginx/access.log main;

    # 启用 Gzip 压缩
    gzip on;
    gzip_disable "msie6";

    # 启用发送文件优化
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;

    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/conf.d/*.conf;

    # 默认服务器配置
    server {
        listen 80 default_server;
        server_name localhost;

        # 站点根目录
        root /usr/share/nginx/html;

        # 主页文件
        index index.html index.htm;

        # 处理静态文件请求
        location / {
            try_files $uri $uri/ =404;
        }

        # 配置反向代理
        # 如果你需要反向代理到一个后端服务，可以启用以下代码：
        # location /api/ {
        #     proxy_pass http://backend_service;
        #     proxy_set_header Host $host;
        #     proxy_set_header X-Real-IP $remote_addr;
        #     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        #     proxy_set_header X-Forwarded-Proto $scheme;
        # }

        # 错误页面
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root /usr/share/nginx/html;
        }
    }
}
```

## 安装nexus 3

```shell
# 创建数据文件夹
mkdir /docker/nexus3/nexus-data

# 如果有权限问题
chmod 777 /docker/nexus3/nexus-data

docker run -d \
     -p 8081:8081 \
     --name nexus \
     -v /docker/nexus3/nexus-data:/nexus-data \
     --restart=always \
     sonatype/nexus3

# 查看密码
cat /docker/nexus3/nexus-data/admin.password
```

## 安装Elastic Search

```
docker run -itd \
    -p 9200:9200 \
    -p 9300:9300 \
    -e "discovery.type=single-node" \
       -v /home/anthony/es/config:/usr/share/elasticsearch/config/ \
       --name elasticsearch \
       --restart=always \
       docker.elastic.co/elasticsearch/elasticsearch:7.7.0
```

## 安装Zookeeper

```
docker run -d -p 2181:2181 --name some-zookeeper --restart=always zookeeper
```

[安装可视化软件](https://github.com/vran-dev/PrettyZoo.git)

mac安装的使用要查看下说明文档,会出现安装包损坏的情况

## 安装Grafana

```
# admin / admin
docker run -d -p 3000:3000 --name=grafana grafana/grafana
```

## 安装WordPress

[docker-compose安装WordPress](https://blog.csdn.net/shangyexin/article/details/106311376)

## 安装禅道

```shell
# 内置数据库
docker run -d -v <你的宿主机目录>/data:/data -p 80:80 -e MYSQL_INTERNAL=true hub.zentao.net/app/zentao 

# 外接数据库,web页面的安装向导,点击确定之后要等一会
docker run -itd \
    -v /docker/zentao/data:/data \
    -p 8001:80 \
    -e MYSQL_INTERNAL=false \
    -e ZT_MYSQL_HOST=172.17.0.3 \
    -e ZT_MYSQL_PORT=3306 \
    -e ZT_MYSQL_USER=root \
    -e ZT_MYSQL_PASSWORD=Qwer1234. \
    -e ZT_MYSQL_DB=zentao \
    --restart=always \
    --name zentao \
    hub.zentao.net/app/zentao 
```

[禅道官方文档](https://www.zentao.net/book/zentaopms/docker-1111.html#15)

## 安装Nacos

mac docker 安装nacos

```shell
docker run -d \
		   -p 8848:8848 \
		   --env MODE=standalone  \
		   --name nacos  \
		   zhusaidong/nacos-server-m1:2.0.3
```

访问地址：http://localhost:8848/nacos/ 

账号/密码：nacos/nacos

> [Nacos官方文档](https://nacos.io/zh-cn/docs/v2/quickstart/quick-start-docker.html)