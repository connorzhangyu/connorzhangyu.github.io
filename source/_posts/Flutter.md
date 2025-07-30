---
title: Flutter
categories:
  - 前端
cover: https://image.runtimes.cc/202407271621772.png
abbrlink: 64595
date: 2024-07-27 16:17:46
updated: 2024-07-28 12:55:32
---

# 安装

以`Ma`为例,安装之后任何地方有问题,要执行flutter自带的命令,检查`flutter doctor`

选择对应的版本,下载到本地就是一个压缩包,解压,就能看到flutter的源码,包含`Dart`SDK

![image-20240727162450510](https://image.runtimes.cc/202407271624215.png)

修改环境变量,记得要`source`下

```shell
export PATH=安装目录/flutter/bin:$PATH
```

Android Studio 安装`Flutter`插件,会自动安装`Dart`插件,新建Flutter项目

![image-20240727162701726](https://image.runtimes.cc/202407271627148.png)

选择flutter下载的目录,最后解压出来的文件夹,后缀加上版本号

![image-20240727162820290](https://image.runtimes.cc/202407271628362.png)

选择要创建哪些平台,看别人的教程,这里还有ios language的选项,不知道这里为啥没有

![image-20240727162913174](https://image.runtimes.cc/202407271629455.png)

点击创建,flutter项目就创建完成了,接下来安装安卓模拟器

![image-20240727163213937](https://image.runtimes.cc/202407271632326.png)

用生成出来的Demo运行下,就能看到例子了,到目前使用的是安卓的模拟器,下面是ios的模拟器

下载xcode,创建一个默认的ios的项目,点击运行,会提示下载模拟器,点下载就好了

![image-20240728122303800](https://image.runtimes.cc/202407281223696.png)

选择模拟器,并重新再运行

![image-20240728122551387](https://image.runtimes.cc/202407281225750.png)

运行成功就会有一个iPhone的界面

![image-20240728122648500](https://image.runtimes.cc/202407281226888.png)

安装命令行工具

![image-20240728122757878](https://image.runtimes.cc/202407281227469.png)

之后再执行`flutter doctor`就能大部分的都解决了,没有解决的,有会相应的命令提示

# 资源 & 媒体

## 添加资源和图片

**指定资源**

Flutter 使用 `pubspec.yaml` 文件，位于项目根目录，来识别应用程序所需的资源。

在根目录下创建`assets`文件夹

```yaml
flutter:
  assets:
	  # 指定单个资源
    - assets/my_icon.png
    # 指定整个目录,需要在目录名称的结尾加上 /：
    - directory/
```

代码使用

```dart
Image.asset("assets/images/star.png", width: 30, height: 30)
```

## 加载网络图片

```dart
Image.network("https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9Gc")
```

> 参考:
>
> [Flutter官网](https://docs.flutter.dev/get-started/install)
