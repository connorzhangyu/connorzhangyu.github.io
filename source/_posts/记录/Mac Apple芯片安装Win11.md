---
title: Mac Apple芯片安装Win11
categories:
  - 记录
cover: https://image.runtimes.cc/202505061512872.webp
abbrlink: 41114
date: 2025-05-06 15:09:33
updated: 2025-05-06 15:09:35
---

# Mac Apple芯片安装Win11

> [M1/M2 Pro VMware Fusion虚拟机安装Win11教程(超详细)_m1装win11_七维大脑的博客-CSDN博客](https://blog.csdn.net/weixin_52799373/article/details/129658881)
>
> [M用户使用VM虚拟机安装win11连不上网，怎么解决_mac_xwz的博客-CSDN博客](https://blog.csdn.net/m0_72489515/article/details/128036581)

![image-20240106171142746](https://image.runtimes.cc/202404051338314.png)

![image-20240106171214171](https://image.runtimes.cc/202404051338837.png)

> 记得要下载ARM版本的

![image-20240106171222884](https://image.runtimes.cc/202404051338380.png)

![image-20240106171238651](https://image.runtimes.cc/202404051339250.png)

这里设置的密码,后面不会再用了,可以设置个简单点的就行

![image-20240106171316780](https://image.runtimes.cc/202404051339063.png)

![image-20240106171329023](https://image.runtimes.cc/202404051340279.png)

![image-20240106171353840](https://image.runtimes.cc/202404051340353.png)

内存设置4GB,硬盘设置成80GB

![image-20240106171404594](https://image.runtimes.cc/202404051340025.png)

![image-20240106171419408](https://image.runtimes.cc/202404051341339.png)

![image-20240106171447630](https://image.runtimes.cc/202404051341466.png)

这里要快速按回车,不然就提示EFI Network,如果是这个提示,重新启动虚拟机就行,之后就是正常安装Window系统的步骤,然后就会遇到连接到网络的问题

![image-20240106172001913](https://image.runtimes.cc/202404051341301.png)

`Shift + F10`打开命令行,输入`OOBE\BYPASSNRO`

![image-20240106174310837](https://image.runtimes.cc/202404051341481.png)

然后系统就行回重启,然后又回到设置Windown的设置开始,这是正常的,然后就一直都是配置,正常配置就好,选择没有连接,就可以创建本地账号

![image-20240106174320712](https://image.runtimes.cc/202404051341581.png)

![image-20240106174335945](https://image.runtimes.cc/202404051341019.png)

![image-20240106174424437](https://image.runtimes.cc/202404051341025.png)

安装完成之后,是没有网络的,需要安装vm-tools

![image-20240106174524696](https://image.runtimes.cc/202404051345365.png)

![image-20240106174545175](https://image.runtimes.cc/202404051346760.png)

![image-20240106174556748](https://image.runtimes.cc/202404051347491.png)

![image-20240106174616569](https://image.runtimes.cc/202404051348197.png)

正常安装软件的步骤就可以了

# 固定IP

配置完了,最好是重启下vmware和虚拟机,和禁用开启 win10上的vmare8网卡

1.vmare配置
![](https://image.runtimes.cc/202404051348330.png)

2.电脑配置,虚拟机配置

![](https://image.runtimes.cc/202404051349925.png)
