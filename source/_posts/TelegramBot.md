---
title: Telegram Bot
categories:
  - 软件
tags:
  - 笔记
  - 教程
cover: https://image.runtimes.cc/202404051534253.png
description: telegram 机器人开发记录
abbrlink: 25532
date: 2023-07-08 15:09:14
updated: 2023-07-17 23:16:00
---

# 机器人设置
## 消息分类
消息分两种:
1. 普通文本消息(比如`test`)
2. 命令消息(以`'/'`开头的文本,比如 `/test` )

默认在群里只能收到命令消息,和机器人私聊可以接收到全部的消息,如果想让机器人在群里也能接收到全部的消息,操作如下
访问`BotFather`,输入`/setprivacy`,选择自己的机器人,选择`DISABLED`,就可以了

