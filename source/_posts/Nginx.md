---
title: Nginx
tags:
  - Mac
categories:
  - Linux
cover: https://image.runtimes.cc/202404051531732.png
abbrlink: 9355
date: 2022-11-19 18:25:00
updated: 2024-08-06 14:07:00
---

## Docker安装 Nginx

```shell
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

## Ubuntu安装Nginx

```bash
# Ubuntu安装依赖
sudo apt-get install libpcre3 libpcre3-dev openssl libssl-dev zlib1g-dev
# Ubuntu 如果还缺少依赖
apt-get install build-essential

# Centos安装依赖
yum install -y gcc-c++ pcre pcre-devel  zlib zlib-devel  openssl openssl-devel

# 进入到解压的文件
cd ./nginx-1.16
./configure --prefix=/url/local/nginx
#或者
./configure
# 如果需要指定模块
./configure --add-module=../nginx-rtmp-module

#然后目录下就多了一个nginx文件
make && make install

#启动
cd ../nginx/sbin
./nginx

# 配置文件目录
所有的配置文件都在/etc/nginx下，并且每个虚拟主机已经安排在了/etc/nginx/sites-available下
程序文件在/usr/sbin/nginx
日志放在了/var/log/nginx中
并已经在/etc/init.d/下创建了启动脚本nginx
默认的虚拟主机的目录设置在了/var/www/nginx-default (有的版本 默认的虚拟主机的目录设置在了/var/www, 请参考/etc/nginx/sites-available里的配置)
```

## Nginx 常用命令

```bash
# 启动nginx
start nginx
# 或者
nginx

# 修改配置后重新加载生效
nginx -s reload

# 重新打开日志文件
nginx -s reopen

# 测试nginx配置文件是否正确
nginx -t -c /path/to/nginx.conf

# 快速停止nginx
nginx -s stop

# 完整有序的停止nginx
nginx -s quit

# 指定配置文件启动
nginx -c /home/anthony/nginx.conf
```

## Nginx 配置模板

```yaml
#user  nobody;

# 设置 <= cpu核数,一般设置成cpu*核数
worker_processes  1;

# 指定错误日志文件存放路径，错误日志级别可选项为【debug|info|notice|warn|error|crit】
error_log  logs/warn.log  warn;
# 指定pid存放路径 nginx启动后的进程ID
pid        logs/nginx.pid;

# 工作模式及连接数上限
#user  nobody;
# 设置 <= cpu核数
worker_processes  1;

# 指定错误日志文件存放路径，错误日志级别可选项为【debug|info|notice|warn|error|crit】
# error_log  logs/error.log  error;
# 指定pid存放路径 nginx启动后的进程ID
# pid        logs/nginx.pid;

# 工作模式及连接数上限
events {
    # 使用网络I/O模型，Linux系统推荐使用epoll模型，FreeBSD系统推荐使用kqueue;window下不指定
    # user epoll;

    # 允许的最大连接数
    worker_connections  1024;
}

# 设定http服务器，利用他的反向代理功能提供负载均衡支持
http {
    # 设定mime类型
    include       mime.types;
    default_type  application/octet-stream;
    # 设定日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    client_header_buffer_size 1k;
    large_client_header_buffers 4 4k;

    # access_log  logs/info.log  main;
    # # 设定access log
    send_timeout 3m;
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    # 这个参数表示http连接超时时间，默认是65s。要是上传文件比较大，在规定时间内没有上传完成，就会自动断开连接！所以适当调大这个时间。
    keepalive_timeout  0;
    keepalive_timeout  5000;

    # 开启gzip模块
    gzip  on;
    gzip_min_length 1100;
    gzip_buffers 4 8k;
    gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php application/javascript application/json;
    output_buffers 1 32k;
    postpone_output 1460;
    server_names_hash_bucket_size 128;
    client_header_timeout 3m;        #调大点
    client_body_timeout 3m;          #调大点
    client_max_body_size 200m;         #主要是这个参数，限制了上传文件大大小
    #client_body_buffer_size 1024;
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;
    gzip_http_version 1.1;
    gzip_comp_level 2;
    gzip_vary on;

 #设定虚拟主机
    server {
        listen      80;
     server_name  ap.mmzcg.com;

        # 设置url编码格式，解决参数中文乱码问题
        charset UTF-8;
     # 设定本虚拟主机的访问日志
        # access_log  logs/host.access.log  main;

        # 对 "/" 启用负载均衡
        location / {
            # 设定代理访问地址
            proxy_pass http://127.0.0.1:30000/;

            # 配置SSL证书
            # ssl_certificate /etc/nginx/conf.d/ssl/STAR.huihuang200.com.crt;
            # ssl_certificate_key  /etc/nginx/conf.d/ssl/STAR.huihuang200.com.key;
            # ssl_session_timeout 5m;
            # ssl_session_cache shared:SSL:10m;
            # ssl_protocols TLSv1 TLSv1.1 TLSv1.2 SSLv2 SSLv3;
            # ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
            # ssl_prefer_server_ciphers on;
            # ssl_verify_client off;

            # 解决ajax跨域问题
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Credentials' 'true';
            add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';

         # 保留用户真实信息
            proxy_set_header Host $host:$server_port;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

            # 允许客户端请求的最大单个文件字节数
            client_max_body_size 100m;
            # 缓冲区代理缓冲用户端请求的最大字节数，可以理解为先保存到本地再传给用户
            client_body_buffer_size 1280k;
            # nginx跟后端服务器连接超时时间，发起握手等候响应超时时间(代理连接超时)
            proxy_connect_timeout 5;
            # 连接成功后 等待后端服务器响应时间 其实已进入后端的排队之中等候处理
            proxy_read_timeout 60;
            # 代理请求缓存区 这个缓存区间会保存用户的头信息一共Nginx进行规则处理 一般只要能保存下头信息即可
            proxy_send_timeout 30;
            # 同上 告诉Nginx保存单个用的几个Buffer最大用多大空间
            proxy_buffer_size 256k;
            proxy_buffers 4 256k;
            # 如果系统很忙的时候可以申请国内各大的proxy_buffers 官方推荐 *2
            proxy_busy_buffers_size 256k;
            # proxy 缓存临时文件的大小
            proxy_temp_file_write_size 256k;
            proxy_next_upstream error timeout invalid_header http_500 http_503 http_404;
            proxy_max_temp_file_size 128m;
        }
    }

}
```

## Nginx日志管理

```nginx
#access_log  logs/access.log  main;

# nginx的默认配置,如果在安装的nginx里没有看到这个,也没有关系
#log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
#                  '$status $body_bytes_sent "$http_referer" '
#                  '"$http_user_agent" "$http_x_forwarded_for"';

server {
    listen  80;
    location / {
        root my;
        index index.html
    }

    #配置日志, main 就是log_format 后面的名字
  	# 该server的访问日志的文件是logs/access.log,使用的是main格式
    access_log  logs/access.log  main;
}
```

推荐的配置

```nginx
log_format  main  
		'$remote_addr - $remote_user [$time_local] "$request" $http_host '
		'$status $request_length $body_bytes_sent "$http_referer" '
		'"$http_user_agent"  $request_time';
```

1. remote_addr : 客户端地址
2. remote_user : 客户端用户名
3. time_local : 服务器时间
4. request : 请求内容，包括方法名，地址，和http协议
5. http_host : 用户请求是使用的http地址
6. status : 返回的http 状态码
7. request_length : 请求大小
8. body_bytes_sent : 返回的大小
9. http_referer : 来源页
10. http_user_agent : 客户端名称
11. request_time : 整体请求延时

日志书写规则

一行要用单引号包起来,一行一个,换行也不需要什么连接的符号

## location匹配

=          精准匹配
不写    一般匹配
~          正则匹配
location proxy_pass 后面的url 加与不加/的区别



## server_name匹配

### 通配符匹配

```nix
server {
    listen       80;
    server_name  *.example.org;
    ...
`
}

server {
    listen       80;
    server_name  mail.*;
    ...

}
```

通配符格式中的`*`号只能在域名的开头或结尾，并且`*`号两侧只能是`.`这些无效:

- `www.*.example.org`
- `w*.example.org`

`*`号可以匹配多个域名部分，`*.example.org`可以匹配到:

- `www.example.org`
- `www.sub.example.org`

`.example.org`是比较特殊的通配符格式, 可以同时匹配

- `example.org`
- `*.example.org`。

### 正则匹配
⁉️ 不适用我自己

### 精确匹配

```visual-basic
server {
    listen       80;
    server_name  example.org  www.example.org;
    ...
}
```

### 特殊匹配格式

1. `server_name ""`; 匹配Host请求头不存在的情况。
2. `server_name "-"`; 无任何意义。
3. `server_name "*";` 它被错误地解释为万能的名称。 它从不用作通用或通配符服务器名称。相反，它提供了server_name_in_redirect指令现在提供的功能。 `现在不建议使用特殊名称“ *”，而应使用server_name_in_redirect指令。`

#### 匹配顺序

1. 精确的名字
2. 以*号开头的最长通配符名称，例如 *.example.org
3. 以*号结尾的最长通配符名称，例如 mail.*
4. 第一个匹配的正则表达式（在配置文件中出现的顺序）

### 优化

1. 尽量使用精确匹配;
2. 当定义大量server_name时或特别长的server_name时，需要在http级别调整server_names_hash_max_size和server_names_hash_bucket_size，否则nginx将无法启动。


# Nginx报错

### 1.服务器重启之后，执行 nginx -t 是OK的，然而在执行 nginx -s reload 的时候报错

`nginx: [error] invalid PID number "" in "/run/nginx.pid"`

```
# 解决办法:
nginx -c /etc/nginx/nginx.conf
nginx.conf文件的路径可以从nginx -t的返回中找到。
nginx -s reload
```

### 2.`a duplicate default server for 0.0.0.0:80`

nginx: [emerg] a duplicate default server for 0.0.0.0:80 in /etc/nginx/sites-enabled/gitlab:10

```
删除/etc/nginx/sites-available/default文件，重新启动服务即可

nginx -t :检查配置文件是否出错
```

### 3.403

```
打开nginx.conf
例子：vim /etc/nginx/nginx.conf
把 user 用户名 改为 user root 或 其它有高权限的用户名称即可
```

# Nginx应用

## Acme.sh的使用

```shell
# 下载软件
curl https://get.acme.sh | sh

# 设置别名 alias:
alias acme.sh=~/.acme.sh/acme.sh 

# 切换 Let's Encrypt,默认使用 ZeroSSL
acme.sh --set-default-ca --server letsencrypt

# 执行,会打印配置域名的TXT
acme.sh --issue -d baidu.me --dns --yes-I-know-dns-manual-mode-enough-go-ahead-please

# 配置好执行,再执行
acme.sh --issue -d baidu.me --dns --yes-I-know-dns-manual-mode-enough-go-ahead-please --renew

# --nginx 就是指定域名的配置文件
Your cert is in: /root/.acme.sh/baidu.me_ecc/baidu.me.cer
Your cert key is in: /root/.acme.sh/baidu.me_ecc/baidu.me.key
The intermediate CA cert is in: /root/.acme.sh/baidu.me_ecc/ca.cer
And the full chain certs is there: /root/.acme.sh/baidu.me_ecc/fullchain.cer

# 查看申请的证书
root@jenkins-jumpserver-nginx:~/.acme.sh# acme.sh --list
Main_Domain             KeyLength  SAN_Domains  CA               Created               Renew
baidu     "ec-256"   no           LetsEncrypt.org  2023-09-17T11:52:22Z  2023-11-15T11:52:22Z

# 续期证书
# 证书默认是90天，如需强制更新证书，则执行以下命令
acme.sh --renew -d abc.xyz --force

# acme.sh会自动为你创建 cronjob, 每天 0:00 点自动检测所有的证书, 
# 如果快过期了, 需要更新, 则会自动更新证书，使用以下命令可查看定时任务
crontab -l

# 升级 acme.sh 到最新
acme.sh --upgrade
```

```shell
# CF
export CF_Key="xxxxxxx" 
export CF_Email="CF账号的邮箱"

acme.sh --issue -d "baidu.com" --dns dns_cf
```

## 建立软连接

```bash
sudo ln -s /etc/nginx/sites-available/domain-one.com /etc/nginx/sites-enabled/
```

## 快速部署静态应用

```nginx
# 首尾配置暂时忽略
server {
        listen       8080;
        server_name  localhost;

        location / {
         		# 设置为个人项目的根目录路径
            root /usr/local/var/www/my-project;
            index  index.html index.htm;
            # vue项目404让前端路由处理
            try_files $uri $uri/ /index.html;
        }
}
# 首尾配置暂时忽略
```

## 跨域

```nginx
# 首尾配置暂时忽略
server {
        listen       8080;
        server_name  localhost;

        location / {
            # 跨域代理设置
            proxy_pass http://www.proxy.com; # 要实现跨域的域名
            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
            add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
        }
}
# 首尾配置暂时忽略
```

```nginx
# 第二种
server {
        listen 80 default_server;
        server_name _ www.*;

        location / {
                index index.html;
                root /home/ubuntu/sabong-server-front;
                try_files $uri $uri/ /index.html;
        }

        location /api/ {

                if ($request_method = 'OPTIONS') {
                        add_header 'Access-Control-Allow-Origin' *;
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,language,If-Modified-Since,Cache-Control,Content-Type';
                        add_header 'Access-Control-Allow-Credentials' true;
                        add_header 'Access-Control-Max-Age' 86400;
                        return 200;
                }
                add_header 'Access-Control-Allow-Origin' *;
                add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS';
                add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,language,If-Modified-Since,Cache-Control,Content-Type';
                add_header 'Access-Control-Allow-Credentials' true;
                add_header 'Access-Control-Max-Age' 86400;

                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
                proxy_set_header X-Nginx-Proxy true;
                proxy_pass http://172.31.41.206:9001/;

        }

        location /ws {
                proxy_pass http://172.31.41.206:9006/ws;
                proxy_http_version 1.1;
                proxy_read_timeout 360s;
                proxy_redirect off;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
                proxy_set_header Host $host:$server_port;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header REMOTE-HOST $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
}
```

## Gzip压缩

```nginx
http {
    # 配置gzip压缩
    gzip  on;
    gzip_min_length 1000; # 设定压缩的临界点
    gzip_comp_level 3; # 压缩级别
    gzip_types      text/plain application/xml; # 要压缩的文件类别
}
```

## 转发Vue项目

```nginx
server {
        listen 80 default_server;

        server_name _;

        location / {
                index index.html;
                root /home/ubuntu/sabong-server-front;
								# nginx 转发给前端判断是不是404
                try_files $uri $uri/ /index.html;
        }

        location /api/ {
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
                proxy_set_header X-Nginx-Proxy true;
                proxy_pass http://172.31.41.206:9001/;
        }
}
```

## 转发Websocket

```nginx
location /ws {
        proxy_pass http://172.31.41.206:9006/ws;
        proxy_http_version 1.1;
        proxy_read_timeout 360s;
        proxy_redirect off;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host:$server_port;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header REMOTE-HOST $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

## 转发Redis

只知道这么配置,不知道怎么给redis配置上域名

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 768;
        # multi_accept on;
}

http{
	# ...
}

# 要跟http统计,旧的版本没有steram模块,需要编译的时候加
# 下载最新的版本肯定是有stream模块的
stream {

    upstream redis {
				# myredis.godjfp.com  要被转发的Redis地址
				# 6379:redis自己设置的端口是多少,就是多少
        server myredis.godjfp.com:6379 max_fails=3 fail_timeout=30s;
    }

    server {
				# 公网访问的端口
        listen 6379;
        proxy_connect_timeout 1s;
        proxy_timeout 3s;
        proxy_pass redis;
    }
}
```

![](https://image.runtimes.cc/202404051446413.png)

## 图片上传

post上传文件，出现413错误码 解决方案

```sql
#允许客户端请求的最大单文件字节数
client_max_body_size 10m; 
#缓冲区代理缓冲用户端请求的最大字节数
client_body_buffer_size 128k; 
```

## HTTPS跳转

```nginx
server {
        listen 80;
        listen 443 ssl;

				# 配置SSL证书
        server_name abc.xyz;

  			# 如果是http,就跳转https,注意nginx配置文件的不同,可能会出现重定向次数过多
        if ($scheme = http) {
            return 301 https://$host$request_uri;
        }
        location / {
                proxy_pass http://192.168.0.2:8084/;
        }
}

# 比如配置强制跳转https,导致重定向次数过多
server {
        listen 80;
        listen 443 ssl;

				# 配置SSL证书
        server_name abc.xyz;

  			# 如果是http,就跳转https,注意nginx配置文件的不同,可能会出现重定向次数过多
        # 比如这个配置 return      301 https://$server_name$request_uri;
        # 比如这个配置 rewrite ^(.*)$  https://$host$1 permanent;
        location / {
                proxy_pass http://192.168.0.2:8084/;
        }
}

# 上面两种导致重定向的次数过多,需要修改配置文件,比如下面的配置文件就可以
server {
    listen 80;
    server_name your_domain.com www.your_domain.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name your_domain.com www.your_domain.com;

    ssl_certificate /path/to/your/certificate.crt;
    ssl_certificate_key /path/to/your/private_key.key;
}

```

## 代理JumpServer

```nginx
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
            # 比如服务器里的JumpServer的端口已经设置成了8083
            proxy_pass http://127.0.0.1:8083/;

            # 按照官方的配置,需要使用WebSocket,如果不配置的话,访问JumpServer网页会提示WebSocket报错
            proxy_http_version 1.1;
            proxy_buffering off;
            proxy_request_buffering off;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
}
```

[官方文档:使用Nginx代理JumpServer](https://docs.jumpserver.org/zh/v3/installation/proxy/?h=nginx#2-nginx)

## 代理Jenkins

```nginx
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        
        server_name _;

        location / {
            proxy_pass http://127.0.0.1:8083/;
        }
}

# 如果使用这样的配置,会有问题,比如配置的域名是baidu.com
# 当访问baidu.com会正常访问,当登录之后,网页地址就变成http://127.0.0.1:8083/
# 需要改成一下配置

server {
        listen 80 default_server;
        listen [::]:80 default_server;
        
        server_name _;

        location / {
                proxy_pass http://127.0.0.1:8080/;
    
    						# 下面这几个配置,访问Github会403
                proxy_http_version 1.1;
                http2_push_preload on; # Enable http2 push
                proxy_set_header   X-Forwarded-Proto  $scheme;
                proxy_set_header   X-Forwarded-For    $remote_addr;
                proxy_set_header   X-Real-IP          $remote_addr;
    
    						# 这个配置又是正常的,要看看这个请求头是干嘛的了
   						  proxy_set_header X-Rea $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
                proxy_set_header X-Nginx-Proxy true;
        }
}
```

```nginx
server {
    listen          80;
    server_name     _;
    location /api/ {
               client_max_body_size 100m;
              client_body_buffer_size 50m;
               proxy_redirect off;
               proxy_set_header Host $host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_pass http://172.31.2.105:8083/;
    }

 		# 代理swagger稳定 openapi3的版本,springboot3的版本
    location /v3/api-docs {
               client_max_body_size 100m;
               client_body_buffer_size 50m;
               proxy_redirect off;
               proxy_set_header Host $host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_pass http://172.31.2.105:8083/v3/api-docs;
    }

    location / {
      try_files $uri $uri/ /index.html;
      root /usr/share/nginx/html/manager/dist/;
      index index.html index.htm;
    }

 }
```

# SRS

## Docker方式运行

```docker
docker run -p 1935:1935 -p 1985:1985 -p 8080:8080 ossrs/srs:latest

# 测试
# 访问:[http://192.168.254.180:8080/](http://192.168.254.180:8080/)
```

## SRS安装参考
[https://cloud.tencent.com/developer/article/1693951](https://cloud.tencent.com/developer/article/1693951)

## Mac改成只播放系统声音
[Mac obs推流直播无声音解决方法_OBS教程_OBS Open Broadcaster Software](http://www.obsproject.com.cn/obs/78.html)

## OBS权限监听浏览器
![](https://image.runtimes.cc/202404051442055.png)