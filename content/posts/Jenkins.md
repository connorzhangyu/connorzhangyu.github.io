---
title: Jenkins
categories:
  - Java
tags:
  - Mac
  - 部署
  - Maven
cover: https://image.runtimes.cc/202404051529846.png
abbrlink: 17834
date: 2022-11-19 18:25:00
updated: 2024-08-10 11:44:46
---

# Root用户权限启动Jenkins

适用于Ubuntu环境下通过`apt-get install -y jenkins`命令在线安装的Jenkins

1.修改配置文件

```bash
vim /etc/default/jenkins

# 将下面两个参数修改为root
# 修改前
JENKINS_USER=$NAME
JENKINS_GROUP=$NAME
# 修改后
JENKINS_USER=root
JENKINS_GROUP=root
```

2.更改目录权限

不知道这步有没有起作用

```bash
cd /var/lib/
chown -R root:root ./jenkins

# 重启
systemctl restart jenkins.service
```

# Jenkins使用教程

## 配置JDK

Manage Jenkins --> Global Tool Configuration

1. 自动安装
2. 手动安装要注意,J`AVA_HOME`的输入框下面，不要有警告或者错误信息，否则就是路径不正确,取消勾选自动安装

## 配置Maven
1. 安装Maven Integration 插件
2. 通常自动安装maven就行了,如果手动安装,`MAVEN_HOME` 输入框下面，不要有警告或者错误信息，否则就是路径不正确

## 获取当前项目名称

在脚本中输入 `$JOB_NAME`就能获取到了

## 卸载jenkins

```shell
//服务
sudo apt-get remove jenkins

//安装包，注意这里如果不是ubuntu那就yum
sudo apt-get remove --auto-remove jenkins

//配置和数据
sudo apt-get purge jenkins

sudo apt-get purge --auto-remove jenkins
```

## 升级jenkins

```shell
# 适用于ubuntu安装的
apt-get upgrade jenkins
```

## 同时构建任务的个数

系统管理-->系统设置-->执行者数量

## 构建成功才继续发包

只是个不大不小的功能,很容易忽略了,在 任务的配置-->Post Steps --> Run only if build succeeds

![image-20240528093819570](https://image.runtimes.cc/202405280938281.png)

## 修改时区

![image-20240809174451156](https://image.runtimes.cc/202408091744504.png)

![image-20240809174606994](https://image.runtimes.cc/202408091746778.png)

## WebHook

使用的场景,Github Push代码之后,自动构建

第一步.jenkins的配置

![image-20240809174000506](https://image.runtimes.cc/202408091740783.png)

第二步.Github的配置

![image-20240809174111087](https://image.runtimes.cc/202408091741395.png)

![image-20240809174235801](https://image.runtimes.cc/202408091742218.png)

第三步.返回jenkins设置

![image-20240809174324197](https://image.runtimes.cc/202408091743820.png)

第四步.push代码,查看有没有正常构建

# Jenkins+Docker部署Maven聚合工程

安装Jenkins

```shell
docker run \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v $(which docker):/usr/bin/docker \
    -v /root/jenkins:/var/jenkins_home \
    --name jenkins \
    --user=root \
    -p 8080:8080 \
    -p 50000:50000 \
    -d \
    -u 0 \
    jenkins/jenkins:jdk11
```

- 第2行是将宿主机的/var/run/docker.sock映射到容器中，这样在容器中运行的docker命令，就会在宿主机上来执行。
- 第3行是将宿主机的docker程序映射进容器中，这样本身没有安装docker的jenkins容器就可以执行docker命令了（事实上容器里是没有运行docker的服务的，我们只是用这个映射进容器的docker来作为客户端发送docker的指令到/var/run/docker.sock而已，而/var/run/docker.sock已经被链接到宿主机了)

Dockerfile
```dockerfile
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD admin-server-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

Maven的pom.xml配置
```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>0.4.13</version>
    <configuration>
        <imageName>springboot/${project.artifactId}</imageName>
        <dockerDirectory>src/main/docker</dockerDirectory>
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}</directory>
                <include>${project.build.finalName}.jar</include>
            </resource>
        </resources>
    </configuration>
</plugin>
```

- `imageName`打的镜像名称,这里的镜像名称是:springboot/admin-server
- `dockerDirectory`指定docker文件夹的位置

Job配置

![](https://image.runtimes.cc/202404051423837.png)

![](https://image.runtimes.cc/202404051424832.png)

![](https://image.runtimes.cc/202404051424206.png)

![](https://image.runtimes.cc/202404051424089.png)

• `-e “SPRING_PROFILES_ACTIVE=prerelease”` ,可以看成是启动jar的时候的,java -jar admin-server.jar –spring.profiles.active=prerelease ,指定运行环境

# 脚本

## 检测容器的运行

```bash
# "=" 两边不能有空格
count=`docker ps -a | grep sabong-admin-controlfront |awk '{print $1}'`
if [ -n "$count" ]; then
        docker stop $count
        docker rm $count
        echo "停止并删除容器"
fi

docker run \
	-itd \
	-p 9011:9011 \
	-e “SPRING_PROFILES_ACTIVE=prerelease” \
	--name admin-server \
	springboot/admin-server
```

## 推送镜像到harbor

```bash
# 打标签
# sabong/sabong-controller-manager:latest 已经build成功了的镜像名字
# 10.0.0.7/library/sabong/sabong-controller-manager:latest   新的镜像名字
docker tag sabong/sabong-controller-manager:latest 10.0.0.7/library/sabong/sabong-controller-manager:latest

# 登录私有仓库
docker login -u admin -p 123456 10.0.0.7

# 推送镜像
docker push 10.0.0.7/library/sabong/sabong-controller-manager:latest
```

## Vue打包成镜像

```bash
npm install
npm run build:prod

# build
# sabong/sabong-admin-controlfront  随便起的
docker build -t sabong/sabong-admin-controlfront --no-cache . 

# 打标签
docker tag sabong/sabong-admin-controlfront:latest 10.0.0.7/library/sabong/sabong-admin-controlfront:latest

# 登录私有仓库
docker login -u admin -p 123456 10.0.0.7

# 推送镜像
docker push 10.0.0.7/library/sabong/sabong-admin-controlfront:latest
```

Dockerfile

```docker
From nginx

COPY dist/ /usr/share/nginx/html/
```


⛔ 这里用的是nginx容器里默认的nginx配置文件,所以这里没有使用自定义的nginx配置

## SpringBoot项目脚本

适用于ssh传输文件,启动脚本

```shell
#!/bin/bash

# 具体不知道是干啥的,加上这个,这个看情况加
# BUILD_ID=DONTKILLME

APP_NAME=xxx.jar

# 杀进程
pid=`ps -ef|grep $APP_NAME|grep -v grep|awk '{print $2}' `
    if [ -n "${pid}" ]; then
    kill -9 $pid
fi

echo '准备启动jar包'

nohup java -jar /root/$APP_NAME --spring.profiles.active=prod &

sleep 3

echo '启动成功'
```



# Maven多模块打包
指定打包项目名
```shell
clean package -pl 模块名 -am
```

配置SSH server

在系统管理-->System--->SSH Server配置

![image-20240319175903145](https://image.runtimes.cc/202404051424215.png)

配置SSH Over FTP publish

![image-20240319184601103](https://image.runtimes.cc/202404051424247.png)

这里`Verbose output in console`的作用是把远程的步骤,和远程的shell执行的结果也打印出来

这里`Exec in pty` 要勾选,不然项目启动完成了,也不会停止SSH链接

![image-20240319175519104](https://image.runtimes.cc/202404051424187.png)

# 前端打包

选择已经配置的node版本

**构建环境**

![image-20240319183930032](https://image.runtimes.cc/202404051424037.png)

**Build Steps**

选择执行shell

```shell
npm install
npm run build:prod
pwd
# 删除上一次构建打的包,然后dist文件夹打包成dist.zip文件
rm -f dist.zip && zip -r dist.zip dist
```

![image-20240319184412134](https://image.runtimes.cc/202404051424098.png)





# 各种问题报错

1.HTTP ERROR 403 No valid crumb was included in the request

解决办法:网上都是要去关闭CSRF，很明确的是我这里的全局安全配置里面根本就没有选项，我的版本是`jenkins 2.293`。所以百度出来的都是一堆垃圾，无奈自己解决，尝试一番之后，找到了方法，如

![image-20231201155420918](https://image.runtimes.cc/202404051425841.png)

下图所示：还是在全局安全配置里面，勾选上这个参数即可。

------

2.Jenkins配置中安装插件时提示`No such plugin: cloudbees-folder`

```bash
sudo systemctl start jenkins
```

------

2.Jenkins和Github personal access token

- 登录到您的 Jenkins 服务器。
- 选择“Jenkins”>“Manage Jenkins”>“Configure System”。
- 滚动到“GitHub”部分。
- 点击“Add GitHub Server”按钮。
- 在“API URL”字段中输入您的 Github API URL（例如 [api.github.com）。](https://link.juejin.cn/?target=https%3A%2F%2Fapi.github.com%EF%BC%89%E3%80%82)
- 在“Credentials”字段中选择“Add”。
- 选择“Kind”为“Username with password”。
- 在“Username”字段中输入您的 Github 用户名。
- 在“Password”字段中输入您的 Github 个人访问令牌。
- 点击“Verify credentials”按钮以验证凭据是否有效。
- 如果验证成功，请点击“Save”按钮保存更改。

3.jenkins映射docker

```bash
# 如果docker run jenkins 没有指定
# 这里就不能打包,就是因为在jenkins里的容器中,没有安装对象,使用了-v 
# 就是把jenkins里所需要的docker映射到宿主docker中
-v /var/run/docker.sock:/var/run/docker.sock \
-v $(which docker):/usr/bin/docker
```

4.安装插件没有成功

![](https://image.runtimes.cc/202404051425983.png)
需要到插件中心,在可更新和可选插件里,一个一个手动去搜索安装,如果还有问题,就需要更新jenkins

5.`npm install`需要权限

```shell
Unable to save binary /var/lib/jenkins/workspace/mymanager-/node_modules/node-sass/vendor/linux-x64-83 : Error: EACCES: permission denied, mkdir '/var/lib/jenkins/workspace/lottery-web-24kai-ausk3/node_modules/node-sass/vendor'
```

# 修改端口

```shell
# 第一步
sudo vim /etc/init.d/jenkins
# 8088改成想要的
check_tcp_port "http" "$HTTP_PORT" "8088" || return 1

# 第二步
sudo vim /etc/default/jenkins
# 8088改成想要的
HTTP_PORT=8088

# 第三步
vim /lib/systemd/system/jenkins.service
Environment="JENKINS_PORT=80"

# 执行
sudo systemctl daemon-reload
sudo systemctl start jenkins
sudo systemctl status jenkins

# 这个时候就启动报错,然后接着修改
vim /lib/systemd/system/jenkins.service
# 本来是jenkins的,改成root
User=root
Group=root

# 执行,就可以了,不知道为啥
sudo systemctl daemon-reload
sudo systemctl start jenkins
sudo systemctl status jenkins

```



# jenkins使用root账号登录

[设置1](https://www.baimeidashu.com/13094.html)

[设置2](https://www.cnblogs.com/fieldtianye/p/17852160.html)

# 权限管理

1.安装插件,`Role-based Authorization Strategy`

![image-20240808165032732](https://image.runtimes.cc/202408081650831.png)

2.点击 管理系统-->安全段落下的-->全局安全配置,选中

![image-20240808165220186](https://image.runtimes.cc/202408081652851.png)

3.管理系统-->安全段落下的 就会多一个功能

![image-20240808165321957](https://image.runtimes.cc/202408081653465.png)

