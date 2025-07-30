---
title: Flarum
tags:
  - Flarum
categories:
  - 记录
cover: 'https://image.runtimes.cc/202505051128639.webp'
abbrlink: 13766
date: 2025-05-04 18:52:43
updated: 2025-05-04 18:10:26
---
# Flarum 部署

- Ubuntu 24.04

- 安装 PHP、MariaDB、Nginx、Composer

- 安装的目录是在`/home/ubuntu/flarum/`

# 安装Flarum

## ✅ 安装PHP

```shell
sudo apt update && sudo apt upgrade -y

sudo apt install -y \
  nginx \
  php \
  php-cli \
  php-mbstring \
  unzip \
  curl \
  php-gd \
  php-mysql \
  php-xml \
  php-curl \
  php-zip \
  php-bcmath \
  php-fpm

# 验证版本
php -v
```

## ✅ 安装 Composer

```shell
cd ~

curl -sS https://getcomposer.org/installer | php

sudo mv composer.phar /usr/local/bin/composer

# 验证版本
composer -V
```
## ✅ 安装 Flarum

```shell
cd ~
mkdir flarum
composer create-project flarum/flarum flarum
```
## ✅ 安装MariaDB

```sql
# 安装数据库
sudo apt install mariadb-server -y
sudo systemctl enable mariadb
sudo systemctl start mariadb

# 初始化,设置密码
sudo mysql_secure_installation

# 登录
sudo mysql -u root -p

# 设置密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyRootPass123!' ;

# 设置远程访问
CREATE USER 'root'@'%' IDENTIFIED BY 'MyRootPass123!';

# 设置权限
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;

# 创建数据库
CREATE DATABASE flarum CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

# 刷新权限
FLUSH PRIVILEGES;

# 退出
EXIT;

# 开启远程访问
sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf
bind-address = 127.0.0.1   ->  bind-address = 0.0.0.0
sudo systemctl restart mariadb
```

## ✅ 配置Nginx

```nginx
server {
    listen 80;
    server_name _;  # ← 如果没有域名，可以写你的公网 IP

    root /home/ubuntu/flarum/public;
    index index.php index.html;

    # URL 重写规则
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # PHP 解析设置
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    # 禁止访问隐藏文件
    location ~ /\.ht {
        deny all;
    }

    # 缓存静态资源
    location ~* \.(?:ico|css|js|gif|jpe?g|png|woff2?|ttf|svg|eot)$ {
        expires 6M;
        access_log off;
        add_header Cache-Control "public";
    }
}
```

## ✅ 修改文件全新

到这一步,使用IP访问,会404,执行以下命令授权

```shell
sudo chown -R www-data:www-data /home/ubuntu/flarum
sudo chmod -R 775 /home/ubuntu/flarum/storage
sudo chmod -R 775 /home/ubuntu/flarum/public/assets
sudo chmod +x /home/ubuntu
```

## ✅ 配置域名

Cloudflare开启了正常CDN,还需要修改php的配置文件
修改flarum目录下的config.php

```shell
vim ~/flarum/config.php

'url' => 'http://<原来的IP>',
修改成
'url' => 'https://forum.example.com',  

php flarum cache:clear
```

# 配置

## 📤配置邮箱

驱动:smtp

主机:smtp.gmail.com

顿口:587

加密协议:tls

用户名:发送验证码的邮箱

密码:Gmail应用程序的专用密码

