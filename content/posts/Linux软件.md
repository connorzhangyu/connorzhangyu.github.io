---
title: Linux软件
tags:
  - Linux
  - 软件
categories:
  - Linux
cover: https://image.runtimes.cc/202404051530296.jpeg
abbrlink: 10379
date: 2022-11-19 18:25:00
updated: 2024-07-01 14:05:48
---

# JumpSserver

## 设置SFTP根目录

3.0版本设置文件管理

克隆一份Linux平台

![image-20230917163307359](https://image.runtimes.cc/202404051426282.png)

![image-20230917163359314](https://image.runtimes.cc/202404051426865.png)

![image-20230917163415467](https://image.runtimes.cc/202404051426331.png)

修改或者创建资产的时候,选择该克隆的平台,在资产这里不能设置SFTP路径

![image-20230917163519193](https://image.runtimes.cc/202404051426116.png)

# 安装vmware tool

```bash
运行 ./vmware-install.pl
一直按yes,按完一段之后就会有暂时的停顿
运行命令 reboot
```

# 安装Wget

最开始的Centos7 感觉什么都没有`wget`也没有

```bash
 #远程下载的工具
 yum -y install wget

 #bash: make: command not found 是因为缺少这些工具
 yum -y install gcc automake autoconf libtool make

 yum -y install wget
```

# 安装LNMP环境

```bash
# 更新源
apt-get update && apt-get dist-upgrade -y

# 安装nginx
apt-get install nginx

# 安装php-fpm和常用php扩展
apt-get install php-fpm php-gd php-mbstring php-curl php-xml php-mcrypt php-mysql php-zip php-json php-redis php-memcached

# 安装mysql
apt-get install mysql-server

# 建立测试站点
# 1.我们在/var/www下面新建一个test目录，作为站点目录。 运行以下命令：
mkdir /var/www/test

# 2.新建php入口文件
echo '<?php echo 1;'  > /var/www/test/index.php

# 3.授权给fpm用户www-data，使fpm进程可以访问站点文件
chown -R www-data:www-data /var/www/test && chmod -R 755 /var/www/test

# 4.设置nginx站点配置,在/etc/nginx/conf.d新增一个test.conf文件，并写入以下内容
# 这个配置表示站点监听80端口，网站根目录为/var/www/test,入口文件为index.php,通过php-fpm进程来执行php脚本。
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /var/www/test;
        index index.php index.html index.htm;
        server_name _;
        location / {
                try_files $uri $uri/ =404;
        }
        location ~ \.php$ {
               include snippets/fastcgi-php.conf;
               fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }
}

# 5.重新加载
nginx -t && nginx -s reload

# 6.访问试试看
```

# Ubuntu卸载Apache

```bash
# 删除apache
$ sudo apt-get --purge remove apache2
$ sudo apt-get --purge remove apache2.2-common
$ sudo apt-get autoremove

# 找到没有删除掉的配置文件，一并删除
$ sudo find  /etc -name "*apache*" -exec  rm -rf {} \;
$ sudo rm -rf /var/www

# 重装apache2
$ sudo apt-get install apache2
$ sudo /etc/init.d/apache2 restart
```

Ubuntu安装软件指定版本号

```bash
# 安装软件的时候,机器上安装的依赖版本可能高于软件本身需要的
The following packages have unmet dependencies:
 libpcre3-dev : Depends: libpcre3 (= 2:8.39-12build1) but 2:8.44-1+ubuntu18.04.1+deb.sury.org+1 is to be installed
                Depends: libpcrecpp0v5 (= 2:8.39-12build1) but 2:8.44-1+ubuntu18.04.1+deb.sury.org+1 is to be installed

sudo apt install libpcrecpp0v5=2:8.39-12build1 libpcre3=2:8.39-12build1
```

# Gitlab安装

[https://ken.io/note/centos7-gitlab-install-tutorial#H3-11](https://ken.io/note/centos7-gitlab-install-tutorial#H3-11)

# Zsh安装

```bash
$ sudo apt-get install zsh
# 验证是否安装成功
$ zsh --version

# 将 Zsh 设为默认 Shell
$ sudo chsh -s $(which zsh)

# 安装oh my zsh
https://ohmyz.sh/

# 注销当前用户重新登录

# 若输出为 /bin/zsh 或者 /usr/bin/zsh 则表示当前默认 Shell 是 Zsh
$ echo $SHELL
```

# 安装confluence

[atlassian的相关软件](https://www.atlassian.com/purchase/?utm_source=pocket_mylist)

[confluence下载地址](https://3reality.tech/downloads/atlassian/?utm_source=pocket_mylist)

安装:

```python
docker run -v \
	/root/confluence/:/var/atlassian/application-data/confluence/ \
	--name="confluence" \
	-d \
	-p 8090:8090 \
	-p 8091:8091 \
	atlassian/confluence-server
```

破解:

1./opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-v2-3.4.1.jar备份
2.atlassian-extras-decoder-v2-3.4.1.jar重命名为atlassian-extras-2.4.jar并下载到本地 （和破解工具放在同一目录）
3.下载破解工具confluence_keygen.jar,在上面的超链接下载

![](https://image.runtimes.cc/202404051426404.png)

4.将已被破解的jar包atlassian-extras-2.4.jar重名为atlassian-extras-decoder-v2-3.4.1.jar

并放置在原路径/opt/atlassian/confluence/confluence/WEB-INF/lib

5.下载mysql-connector-java-5.1.32-bin.jar 上传/opt/atlassian/confluence/confluence/WEB-INF/lib

6.开始安装到需要授权码

![](https://image.runtimes.cc/202404051426338.png)

![](https://image.runtimes.cc/202404051427340.png)

相关报错:

```python
启动发生报错 "Spring Application context has not been set"
rm -f /var/atlassian/application-data/confluence/confluence.cfg.xml
```

# Nmap
## 找出活跃的主机

```
nmap -sP 192.168.41.136

nping --echo-client "public" echo.nmap.org
```

## 查看打开的端口

nmap 192.168.41.136

# 网络软件

[清理网络DNS缓存](https://one.one.one.one/purge-cache/)

[检查nameserver有没有生效](https://dnschecker.org/ns-lookup.php?query=55lukcy.in&dns=cloudflare)

[DNS生效的国家](https://www.whatsmydns.net/#CNAME/whatsmydns.net)

[IP查询](https://www.ip2location.com/demo/92.98.82.166)

[发送文件到Kindle](https://www.amazon.com/sendtokindle)



# VIM
## vim的配置文件设置

```bash
# 先复制一份vim配置模板到个人目录下
cp /usr/share/vim/vimrc ~/.vimrc

# 编辑
vi ~/.vimrc

# 添加
syntax on # 开启高亮
set nu!   # 默认开启行号
```

## 行号

```bash
set nu
```

## 过滤注释

```bash
 cat redis.conf |grep -v '#' |grep -v '^$' >redis-6237.conf
```

## 多行注释

```bash
1 Ctrl+v进入v模式
2 上下方向键选中要注释的行
3 shift+i（即大写的I）行首插入
4 输入注释符//
5 按esc返回
```

## 反注释

```bash
1 Ctrl+v进入v模式
2 上下方向键选中要注释的行，左右键选择要删除的字符//
3 按d删除
```

## 格式化

```bash
# gg 跳转到第一行
# shift+v 转到可视模式
# shift+g 全选
# 按下=号
```

## 删除#开头的行

```bash
:g/^#/d
```


# SSH
## 安装ssh

```bash
# 判断Ubuntu是否安装了ssh服务,检查有没有可以看到“sshd”
ps -e | grep ssh

# 安装ssh服务，输入命令
sudo apt-get install openssh-server

# 启动服务
sudo /etc/init.d/ssh start
```

## SSH配置文件设置

```bash
vim /etc/ssh/sshd_config

# 这是设置允许root登陆
PermitRootLogin prohibit-password ===> PermitRootLogin yes

# 这是设置允许使用账号密码登录
# 不能只注释,注释会不起作用,还是可以用密码登录
PasswordAuthentication no ===> PasswordAuthentication yes

# 允许用证书登录方式
PubkeyAuthentication yes

# 重启ssh服务
sudo service ssh reload
```

## SSH连接,key失效

```bash
λ ssh root@10.19.44.12
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:RIqSp3Pd94k4PPQcDtKAzK0sQbf4VWffKGwy/nvO8Kw.
Please contact your system administrator.
Add correct host key in C:\\Users\\Owner/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in C:\\Users\\Owner/.ssh/known_hosts:12
ECDSA host key for 10.19.44.12 has changed and you have requested strict checking.
Host key verification failed.
```

解决方法:找到.ssh文件下的known_hosts,删除相关ip的行

## 生成证书

```shell
# 客户端生成秘钥
ssh-keygen

# ssh目录的文件
# id_rsa,私钥文件。不应该分享给任何人。私钥文件通常以 PEM 格式存储。
# id_rsa.pub,公钥文件。公钥可以公开分享，用于配置在你需要访问的服务器上。服务器使用公钥来加密信息，私钥可以解密。
# known_hosts, 文件存储了你之前连接过的所有服务器的主机密钥。这些密钥用于验证服务器的身份，以防止中间人攻击。每次你连接一个新的服务器，服务器的公钥会被添加到这个文件中。
# known_hosts.old,是 `known_hosts` 文件的备份。每当 `known_hosts` 文件更新时，旧版本会被保存为 `known_hosts.old`。这可以作为一个安全机制，如果新的 `known_hosts` 文件出现问题，可以回滚到旧版本。
➜  .ssh ll /Users/anthony/.ssh
-rw-------@ 1 anthony  staff   1.6K  6  8 21:50 id_rsa    
-rw-r--r--@ 1 anthony  staff   425B  6  8 21:50 id_rsa.pub
-rw-------@ 1 anthony  staff   3.0K  6  8 21:24 known_hosts			
-rw-------@ 1 anthony  staff   2.3K  6  8 21:23 known_hosts.old

# 把公钥拷贝到服务器,这次是要输入密码
ssh-copy-id -i ~/.ssh/id_rsa.pub user@remote_host

# 使用证书登录服务器
# 实验的时候有个问题,如果不指定证书登录的命令,还是会提示输入密码
ssh -i ~/.ssh/id_rsa_server2 ubuntu@your_server_ip2
```

## root账号登录并且配置证书

```shell
# 已经使用别的用户(账户名是:anthony)登录了服务器之后,再执行
sudo passwd root
# 第一次输入anthony用户的密码
# 第二次输入新密码
# 第三次再次输入新密码

# 修改配置文件,让root用户可以登录,看上面的配置

# 客户端上传rsa公钥,留意这里是root的账户的路径
cat ~/.ssh/id_rsa.pub | ssh root@172.16.147.129 'cat >> /root/.ssh/authorized_keys'
# 然后就可以使用root账户和证书登录了

# 证书本身是不区分用户名,只认是不是一对公钥还是私钥
```

## root登录之后没有高亮

```bash
cp /home/kali/.bashrc /root
source .bashrc
```


## oh-my-zsh

### 安装oh-my-zsh
```bash
# 配置文件位置
vim ~/.zshrc

# 配置主题
# jonathan 是主题的名字
ZSH_THEME="jonathan"

# 配置插件,空格分开 插件的名字
plugins=(git zsh-syntax-highlighting zsh-autosuggestions)
```


### oh my zsh插件

#### zsh-syntax-highlighting

高亮语法，如图，输入正确语法会显示绿色，错误的会显示红色，使得我们无需运行该命令即可知道此命令语法是否正确

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

plugins=(其他的插件 zsh-syntax-highlighting)

#### zsh-autosuggestions

自动补全

只需输入部分命令即可根据之前输入过的命令提示，按右键→即可补全

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

plugins=(其他的插件 zsh-syntax-highlighting)


# Shell

## 设置别名

经常要用命令行,进入到某个目录

1. 编辑你的 Shell 配置文件，例如 Bash 的配置文件是 `~/.bashrc` 或 `~/.bash_profile`，Zsh 的配置文件是 `~/.zshrc`。
2. 添加 `alias work='cd /path/to/your/work/directory'`
3. 运行 `source ~/.bashrc` 或 `source ~/.zshrc` 来使配置生效。
4. 终端中输入 `work` 命令，就会进入你设置的工作目录

## 设置环境变量

```bash
# 设置环境变量
export http_proxy=http://127.0.0.1:3128

# 删除环境变量
unset http_proxy
```

## curl换行

```bash
curl http://www.baidu.com -w '\n'
```

## curl请求不校验证书

```bash
# 报错信息
# curl failed to verify the legitimacy of the server and therefore could not
# establish a secure connection to it. To learn more about this situation and
# how to fix it, please visit the web page mentioned above.

curl -k https://random.com
# 或者
curl -insecure https://random.com
```

## 脚本首行

```bash
#!/bin/bash
```

## 脚本变量

```bash
# shell脚本给变量赋值的时候 = 两边不能加空格
your_name="runoob"
# 使用双引号拼接
greeting="hello, "$your_name" !"
```

## 判断

[Linux shell脚本之 if条件判断_doiido的专栏-CSDN博客](https://blog.csdn.net/doiido/article/details/43966819)

## 判断数值

| 命令              | 解释                           |
| ----------------- | ------------------------------ |
| [ INT1 -eq INT2 ] | INT1和INT2两数相等返回为真 ,=  |
| [ INT1 -ne INT2 ] | INT1和INT2两数不等返回为真 ,<> |
| [ INT1 -gt INT2 ] | INT1大于INT2返回为真 ,>        |
| [ INT1 -ge INT2 ] | INT1大于等于INT2返回为真,>=    |
| [ INT1 -lt INT2 ] | INT1小于INT2返回为真 ,<        |
| [ INT1 -le INT2 ] | INT1小于等于INT2返回为真,<=    |

## 字符串判断

| 命令                   | 解释                                              |
| ---------------------- | ------------------------------------------------- |
| [ -z STRING ]          | 如果STRING的长度为零则返回为真，即空是真          |
| [ -n STRING ]          | 如果STRING的长度非零则返回为真，即非空是真        |
| [ STRING1 ]            | 如果字符串不为空则返回为真,与-n类似               |
| [ STRING1 == STRING2 ] | 如果两个字符串相同则返回为真                      |
| [ STRING1 != STRING2 ] | 如果字符串不相同则返回为真                        |
| [ STRING1 < STRING2 ]  | 如果 “STRING1”字典排序在“STRING2”前面则返回为真。 |
| [ STRING1 > STRING2 ]  | 如果 “STRING1”字典排序在“STRING2”后面则返回为真。 |

```bash
#!/bin/sh

STRING=

if [ -z "$STRING" ]; then
    echo "STRING is empty"
fi

if [ -n "$STRING" ]; then
    echo "STRING is not empty"
fi

# 结果是:STRING is empty
```

