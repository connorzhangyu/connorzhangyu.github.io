---
title: 使用 Cloudflare 和 Nginx 构建高性能安全站点
id: 0801f36b-b773-4333-bd81-bd4faf2a6c08
date: 2024-12-07 18:13:27
auther: connor
cover: null
excerpt: 使用 Cloudflare 和 Nginx 构建高性能安全站点 在现代网站架构中，Nginx 和 Cloudflare 是两大关键技术，它们分别在服务器端和网络加速层提供强大的支持。本篇博客将带你一步步配置 Nginx 与 Cloudflare 的集成，打造一个高性能且安全的生产环境。 一、为什么选
---


# 使用 Cloudflare 和 Nginx 构建高性能安全站点

在现代网站架构中，**Nginx** 和 **Cloudflare** 是两大关键技术，它们分别在服务器端和网络加速层提供强大的支持。本篇博客将带你一步步配置 Nginx 与 Cloudflare 的集成，打造一个高性能且安全的生产环境。

---

## 一、为什么选择 Nginx 和 Cloudflare？

- **Nginx**：以其轻量级、高并发性能著称，常用于反向代理、静态文件服务和负载均衡。
- **Cloudflare**：提供免费 CDN 加速、DDoS 防护、DNS 服务以及 HTTPS 加密服务，是全球站点优化的理想选择。

当二者结合时，Cloudflare 位于网络层，负责加速和安全，而 Nginx 位于服务器端，负责处理业务逻辑和请求分发。

---

## 二、Nginx 的安装与基本配置

### 1. 安装 Nginx
以 Ubuntu 为例：
```bash
sudo apt update
sudo apt install nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```
安装完成后，通过浏览器访问服务器 IP，确认是否能看到默认欢迎页。

### 2. 配置 Nginx 基本站点
在 `/etc/nginx/conf.d/` 目录下创建站点配置文件：
```bash
sudo nano /etc/nginx/conf.d/example.com.conf
```
内容如下：
```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/example;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
保存文件后重载 Nginx 配置：
```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## 三、Cloudflare 的配置与优化

### 1. 将域名接入 Cloudflare
1. 注册并登录 Cloudflare。
2. 添加你的域名。
3. 修改域名 DNS 到 Cloudflare 提供的解析服务器。
4. 启用代理（橙色小云），Cloudflare 开始加速你的域名。

### 2. 配置 SSL 模式
在 Cloudflare 面板中，前往 **SSL/TLS > Overview**，选择适合你的模式：
- **Flexible**：仅 Cloudflare 和客户端之间使用 HTTPS（不推荐）。
- **Full**：Cloudflare 和 Nginx 之间使用 HTTPS，但无需有效证书。
- **Full (Strict)**：最安全模式，要求 Nginx 提供有效的 SSL 证书。

---

## 四、Nginx 与 Cloudflare 的集成配置

### 1. 获取真实客户端 IP
Cloudflare 代理后，Nginx 默认会记录 Cloudflare 的服务器 IP。为解决这个问题，在 Nginx 的 `http` 块中添加以下配置：
```nginx
http {
    set_real_ip_from 103.21.244.0/22;
    set_real_ip_from 104.16.0.0/12;
    set_real_ip_from 108.162.192.0/18;
    # 更多 IP 请参考 Cloudflare 文档
    real_ip_header CF-Connecting-IP;
}
```

### 2. 配置 HTTPS 支持
如果选择了 **Full (Strict)** 模式，Nginx 必须配置 SSL：
```nginx
server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate /etc/ssl/certs/example.com.crt;
    ssl_certificate_key /etc/ssl/private/example.com.key;

    root /var/www/example;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
此外，为提升安全性，建议启用 HSTS（严格传输安全策略）：
```nginx
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
```

### 3. 配置缓存与压缩
启用缓存头，优化静态资源缓存：
```nginx
location / {
    add_header Cache-Control "public, max-age=3600";
    expires 1h;
}
```
启用 Gzip 压缩：
```nginx
http {
    gzip on;
    gzip_types text/plain application/json application/javascript text/css;
}
```

### 4. 代理 WebSocket 和 API
如果有 WebSocket 或 API 后端，确保代理正确转发请求：
```nginx
location /ws/ {
    proxy_pass http://127.0.0.1:8080;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}

location /api/ {
    proxy_pass http://127.0.0.1:8081;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

---

## 五、生产环境中的安全与优化

### 1. 防火墙规则
仅允许 Cloudflare 的 IP 访问你的服务器：
```bash
sudo ufw allow proto tcp from 103.21.244.0/22 to any port 443
```

### 2. 日志管理
启用详细日志便于排查问题：
```nginx
access_log /var/log/nginx/access.log;
error_log /var/log/nginx/error.log;
```

### 3. 监控性能
使用工具如 `htop` 和 `ngxtop` 实时监控 Nginx 的性能。

### 4. 配置 Brotli 压缩
如果 Cloudflare 启用了 Brotli，Nginx 也可支持：
```nginx
http {
    brotli on;
    brotli_types text/plain application/json application/javascript text/css;
}
```

---

## 六、总结

通过将 Cloudflare 与 Nginx 集成，你可以大幅提升站点的性能与安全性。Cloudflare 提供了强大的网络层加速和防护，Nginx 则负责高效处理请求和业务逻辑。

无论是个人博客还是企业站点，这种架构都是现代 Web 开发的最佳选择。如果你有任何问题或更好的建议，欢迎在评论区讨论！ 😊