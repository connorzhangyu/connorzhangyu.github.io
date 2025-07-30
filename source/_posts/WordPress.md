---
title: WordPress
categories:
  - Linux
tags:
  - WordPress
  - 软件
cover: https://image.runtimes.cc/202404051535988.png
description: WordPress爬坑笔记
keywords:
  - PHP
  - WordPress
  - linux
abbrlink: 50793
date: 2023-06-16 13:39:21
updated: 2023-07-01 19:13:37
---

# 搭建
使用阿里云,或者腾讯等轻量云服务器,一键搭建

# 教程
## WordPress地址和站点地址的含义和区别
WordPress 地址
没有子站点就忽略,和站点地址一样就行
站点地址
就是浏览器地址栏输入的地址

如果地址填入错误,导致不能进入网站,需要手动修改数据库
```sql
select option_name,option_value from options where option_name = 'siteurl' or option_name='home'
```

## 在云服务器控制面板强制重启,导致数据无法启动
```shell
rm -f /www/server/data/ib_*
rm -f /www/server/data/mysql-bin*
```

## 常用的插件

| 插件                           |
| :----------------------------- |
| Elementor                      |
| Essential Addons for Elementor |
| SMTP Mailer                    |
| Starter Templates              |
| Yoast SEO                      |