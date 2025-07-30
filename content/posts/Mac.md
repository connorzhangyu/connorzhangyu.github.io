---
title: Mac
id: 24213fcc-a199-43b2-96ed-f11244f571fa
date: 2025-01-04 03:56:02
auther: connor
cover: null
excerpt: title Mac tags Mac 使用 教程 Github categories 操作系统 cover https//image.runtimes.cc/202404051524065.jpg abbrlink 44793 date 2022-11-19 182500 upda
permalink: /archives/45fc5ced-dacb-41f9-87c3-962b0900e5b2
---

---
title: Mac
tags:
  - Mac
  - 使用
  - 教程
  - Github
categories:
  - 操作系统
cover: https://image.runtimes.cc/202404051524065.jpg
abbrlink: 44793
date: 2022-11-19 18:25:00
updated: 2024-01-10 15:36:22
---



# 快捷键

| 描述   | 命令                              |
| ----------------------- | -------------------------------------- |
| 一个应用多个窗口 切换   | command+`                              |
| 录屏幕                  | shift+command+5                        |
| 截图                    | shift+command+4                        |
| 重装系统                | 先一直按住不要松手:Common+R,<br />再按开机键;非苹果键盘,按住微软键+R |
| 上一级目录              | command+↑                              |



# 网站链接
* [一招解决 Chrome / Edge 浏览器卡顿变慢视频掉帧问题 - 让浏览器重回丝般流畅](https://www.iplaysoft.com/fix-chrome-slow.html)
* [AltTab](https://alt-tab-macos.netlify.app/)


# iphone强制重启
![](https://image.runtimes.cc/202404051349933.webp)

# 浏览器返回手势
![](https://image.runtimes.cc/202404051350113.png)

# 放歌有噪音
通过【活动监视器】程序终止【coreaudiod】进程：
1. 聚焦搜索】中输入【activity monitor】
2. 在窗口右上角的搜索栏里输入【coreaudiod】
3. 选择【coreaudiod】进程后，点击窗口上方工具栏中的【停止按钮】（按钮图标为X）



# 只有mac不能访问github

1. 打开`https://github.com.ipaddress.com/`

2. 打开`https://fastly.net.ipaddress.com/github.global.ssl.fastly.net#ipinfo`

3. sudo vim /etc/hosts
    ```Shell
    # 这个ip从第一步获取到的
    140.82.112.4 github.com
    # 这个ip从第二步获取到的
    199.232.69.194 github.global.ssl.fastly.net
    ```
4. 刷新:`sudo killall -HUP mDNSResponder;say DNS cache has been flushed`

# 启动台有问号的图标,点击没反应

```shell
# 执行了之后,自己新建的文件夹会重置
defaults write com.apple.dock ResetLaunchPad -bool TRUE
killall Dock
```

# Mac OS X 访问 Windows共享文件夹

![](https://image.runtimes.cc/202404051351999.png)

在Mac中，点击 Finder菜单的“前往” > “前往服务器”。在弹出的连接服务器对话框中输入「smb://Windows主机的IP地址」，点击“连接”。

![](https://image.runtimes.cc/202404051352087.png)

一般需要输入Windows对应用户的名称和密码，然后弹出如下窗口，选择对应的文件夹，点击“好”即可进行远程访问

![](https://image.runtimes.cc/202404051352069.png)

# 只使用搜狗输入法

系统偏好设置 >> 键盘 >> 快捷键 >> 反选切换输入法的快捷键

![示例图片](https://image.runtimes.cc/202404051353563.png)

# 网络踪迹

```shell
traceroute ap-southeast-1.signin.aws.amazon.com
```

# 安装mysql客户端

```shell
brew install mysql-client

# 安装目录在
/opt/homebrew/Cellar/mysql-client/8.0.33_1 

# mysqldump的目录
/opt/homebrew/opt/mysql-client/bin/mysqldump
```

# Iterm2
## Iterm2多窗口同时输入命令
`只需要输入快捷键 ⌘(command) + ⇧(shift) + i`

## 调出复制过的文本历史
快捷键：“shift+cmd+h”

## 按键回放
回放一段时间内的你敲过的所有字符。
快捷键：“cmd+alt+b”，如图会弹出一个进度条，按左右键就可以实现按键回放了。

# CheatSheet
```shell
brew install cheatsheet

# 使用
随便在一个软件中 长按`command`
```

# Navicat破解
[gitee脚本](https://gitee.com/ProgHub/unlimited_trial_navicat_premium)

# mac安装 protobuf

```shell
brew install protobuf
protoc --version
/opt/homebrew/share/emacs/site-lisp/protobuf
```

# 视频格式转换

```shell
brew install ffmpeg
# 使用这个命令
ffmpeg -i input.mov -c copy output.mp4    
# 不要用这个命令,网上说是速度很慢
ffmpeg -i input.mov output.mp4 
```
