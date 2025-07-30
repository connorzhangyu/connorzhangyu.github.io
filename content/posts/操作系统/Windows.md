---
title: Windows
tags:
  - Windows
  - 命令行
  - 图床
categories:
  - 操作系统
cover: https://image.runtimes.cc/202404051524170.jpg
abbrlink: 27678
date: 2022-11-19 18:25:00
updated: 2023-11-07 17:31:26
---

# Windows Server
## 开放指定端口

![1](https://image.runtimes.cc/202404051353354.webp)

![2](https://image.runtimes.cc/202404051353503.webp)

![3](https://image.runtimes.cc/202404051353919.webp)

![4](https://image.runtimes.cc/202404051353502.webp)

![6](https://image.runtimes.cc/202404051354921.webp)

![7](https://image.runtimes.cc/202404051355944.webp)

## 服务器禁用IPV6

![](https://image.runtimes.cc/202404051355530.png)

⛔ 一定要重启

[官方文档](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/configure-ipv6-in-windows)

## IE浏览器允许下载
通常服务器不允许乱下载软件,所以一般默认被禁用,下面两种方法都行

![](https://image.runtimes.cc/202404051355878.png)

![](https://image.runtimes.cc/202404051355807.png)

# 安装win10系统,创建本地账号

在提示需要账号登录的时候,按`Shift + F10`打开命令行,输入`oobe\bypassnro.cmd`，就会重启,再安装，就不会要求输入微软账号了；也有例外的情况，用过win11商业版的镜像安装的时候,用这个命令不起作用
![win11](https://image.runtimes.cc/202404051355254.png)

> 参考:[系统安装跳过登录](https://blog.csdn.net/qq_33427869/article/details/127573950)

# Powershell改变默认编码
中文版Powershell默认编码为GB2312，而编程,运维,开发中常用编码格式为UTF-8。
## 临时修改
```Powershell
chcp 65001
```

## 永久修改
进入注册表`win+R->输入regedit`在`HKEY_CURRENT_USER\Console`，双击`CodePage`修改键值为`65001`
![](https://image.runtimes.cc/202404051355297.png)
检查是否生效: 新开一个PowerShell,或者重启


# Win10杀线程

```nix
# 查找5091的端口
netstat -aon | find "5091"
# 杀5091的PID
taskkill /f /pid 6880
```

# Win10安装Scoop包管理器

Scoop是windows的包管理器

```bash
# 使用了PowerShell在你当前Windows的账户下
set-executionpolicy remotesigned -s cu

# 在PowerShell下输入
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

# Windows Terminal策略

以管理员身份打开PowerShell 输入

```bash
set-executionpolicy remotesigned
```

# Win10去掉小箭头

```bash
# 去掉小箭头
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Icons" /v 29 /d "%systemroot%\system32\imageres.dll,197" /t reg_sz /f
taskkill /f /im explorer.exe
attrib -s -r -h "%userprofile%\AppData\Local\iconcache.db"
del "%userprofile%\AppData\Local\iconcache.db" /f /q
start explorer
pause

# 回复小箭头
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Icons" /v 29 /f
taskkill /f /im explorer.exe
attrib -s -r -h "%userprofile%\AppData\Local\iconcache.db"
del "%userprofile%\AppData\Local\iconcache.db" /f /q
start explorer
pause
```

复制上面的代码。新建一个文本文件。粘贴后另存为.bat文件，然后以管理员身份打开。

# Win10安装concfg,用来清空掉命令行和powershell的多配置文件

github仓库:`https://github.com/lukesampson/concfg`

```bash
# 安装concfg软件
scoop install concfg

# 删除掉多余的配置文件
concfg clean

# 导入内置的主题配置,还有别的配置
concfg import solarized-dark
```

# Cmd和Powershell的默认值和属性的区别

默认值和属性的不同设置,从哪里启动有关系,比如用开始菜单的 和 从shift + 右键,加载的命令行不一样,还是安装好concfg软件,把多余的都删掉,只留一个

如果你打开 PowerShell 快捷方式时不小心右键修改它的“属性”中的设置项的话，那么它的表现又会不同了。所以如果想一直保持一致，最好不要动任何一个控制台实例的“属性”。`https://www.zhihu.com/question/63867578/answer/220101109`

# Powershell美化教程

用的是这三个地址,等下回安装的时候再写的详细点

```bash
https://zhuanlan.zhihu.com/p/51901035
https://blog.walterlv.com/post/beautify-powershell-like-zsh.html
https://coolcode.org/2018/03/16/how-to-make-your-powershell-beautiful/
```


# 把软件加入到自启动

在运行里输入`shell:startup`,把快捷键拖到打开的文件夹里

# WSL2安装

1.开启
![](https://image.runtimes.cc/202404051355619.png)
2.下载安装镜像
![](https://image.runtimes.cc/202404051357406.png)

![](https://image.runtimes.cc/202404051357977.png)
3.设置wsl2

![](https://image.runtimes.cc/202404051358316.png)

```
PS C:\Users\anthony> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-20.04    Stopped         2
```


# Docker和VMware的虚拟机冲突
把Windows自带虚拟机删除掉
![](https://image.runtimes.cc/202404051358708.png)



再用管理员的powershell执行`bcdedit /set hypervisorlaunchtype off`


# EverythingToolbar

`https://github.com/stnkl/EverythingToolbar`



# PicGO+Github

生成token:`在github->setting->developer settings 选择generate new token`
![](https://image.runtimes.cc/202404051358110.png)

`记得保存好密码,只会创建成功后显示,以后就看不到了`

PicGo设置

```
仓库名:YangAnLin/images
分支名:master
设定token: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
设定自定义域名:https://cdn.jsdelivr.net/gh/YangAnLin/images
```

# 通过修改注册表更改IE浏览器的代理设置

打开注册表

```
regedit或regedit.exe、regedt32或regedt32.exe
```



# Xshell输入删除回车变成^H
File-->Properties-->Terminl里选择Keyboard，然后在Backspace key sequence里选择`ASCII 127`

# WhatsApp
[申请测试版](https://faq.whatsapp.com/web/download-and-installation/how-to-join-or-leave-the-multi-device-beta/?lang=zh_cn)


# Vscode

## 不同的窗口配置不同的主题
在`settings.json`中添加
```json
"workbench.colorTheme": "GitHub Light"
```


## 快捷键
|Win命令|Mac命令|描述|
|---|---|---|
|`Ctrl + J` |`Command + J` |隐藏/显示终端|
|`Ctrl + P` |`Command + P` |快速打开文件|
| |`Alt + Shift`+鼠标移动 |列编辑|
| |`Alt`+鼠标点选 |列编辑+点选|

## 设置
### Python自动补全函数
```go
settings—>addBrackets
```

### Go自动补全函数
```bash
# 这个选项打勾
useCodeSnippetsOnMethodSuggest":true
# 或者搜索suggest相关的设置
```

### 隐藏目录
![](https://image.runtimes.cc/202404051358058.png)

### 文本乱码和重新编码
比如打开的一个文本是GBK编码的文件,用vscode打开的是乱码
如果想要正常显示,先选择文件本来的编码
![](https://image.runtimes.cc/202404051358762.webp)
![](https://image.runtimes.cc/202404051519153.webp)

如果想要把GBK编码的文件,转成UTF-8的编码格式,再保存
![](https://image.runtimes.cc/202404051358732.webp)
![](https://image.runtimes.cc/202404051519346.webp)

### 使用制表符而不是空格

在设置你搜索`indent`，找到 `Editor:Detect Indexntation` 和 `Editor：Insert Spaces` 两个勾选框，取消勾选
### 显示空格和制表符

打开setting

* 在搜索框中输入renderControlCharacters,选中勾选框,即可显示tab.

* 在搜索框中输入renderWhitespace,选择all,即可显示空格.

### 设置制表符为4个空格

打开setting，搜索`tab size`为4，如果不起作用，接下来要设置

搜索`Detect Indentation` 取消勾选，或者勾选试试

## 插件
### Write Timestamp
作用:插入当前时间
`Ctrl + Shift + T`

![Write Timestamp](https://image.runtimes.cc/202404051359789.png)

也可以自己修改时间格式:
![](https://image.runtimes.cc/202404051359630.png)

### PicGO

1.在插件那搜索picgo,安装好插件在插件那里点设置2.配置阿里云oss,参考:`https://picgo.github.io/PicGo-Doc/`

```
{
  "accessKeyId": "",
  "accessKeySecret": "",
  "bucket": "", // 存储空间名
  "area": "", // 存储区域代号
  "path": "", // 自定义存储路径
  "customUrl": "" // 自定义域名，注意要加http://或者https://
}
```

3.通过三个组合键，可以分别从：1.（Ctrl+alt+U）剪切板 2.（Ctrl+alt+E）文件夹 3.（Ctrl+alt+O）指定路径
