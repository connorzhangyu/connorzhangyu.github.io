---
title: Waline
id: d9faf7a4-78af-4a95-9812-119c68e23186
date: 2025-01-04 03:56:02
auther: anthony
cover: null
excerpt: title Waline部署 tags Hexo Waline categories 记录 cover https//image.runtimes.cc/202404051525995.png abbrlink 8693 date 2023-07-25 000546 updated
---

---
title: Waline部署
tags:
  - Hexo
  - Waline
categories:
  - 记录
cover: https://image.runtimes.cc/202404051525995.png
abbrlink: 8693
date: 2023-07-25 00:05:46
updated: 2023-07-25 00:05:44
---
查看Waline文档,并点击`Deploy`,文档上有大概步骤,也可以参考,要使用免费的数据库,我就选择`MongoDB`,可以免费申请

[Waline-Vercel部署](https://waline.js.org/guide/deploy/vercel.html)

部署Waline到Vercel
![image-20230724225259528](https://image.runtimes.cc/202404051403234.png)

![image-20230724225326229](https://image.runtimes.cc/202404051404626.png)

打开Vercel分配的地址,测试评论,就会显示下图,是因为需要配置数据库
![image-20230724225745349](https://image.runtimes.cc/202404051404342.png)

申请免费的`MongoDB`

访问[MongoDB官网](https://www.mongodb.com/zh-cn),并点击免费开始使用,跳转到注册页面,点击使用Google账号登陆,也可以自己注册,这里是为了方便使用Google登陆,登陆之后需要同意一些协议,和调查,随便填写,没有很大关系

![image-20230724225938394](https://image.runtimes.cc/202404051404429.png)

选择免费的,并点击下面的Create

![image-20230724230315868](https://image.runtimes.cc/202404051404269.png)

![image-20230724230343845](https://image.runtimes.cc/202404051404594.png)

等待一会,会自动开始创建

![image-20230724230509021](https://image.runtimes.cc/202404051404821.png)

创建成功,然后点击,Connect,设置ip,设置任何地方都可以访问

点击Choose a connection method,在弹出的窗口选择`Compass`

![image-20230724231046357](https://image.runtimes.cc/202404051404159.png)

获取链接地址,这里有个坑,Waine使用的包比较旧,新版本的协议是:mongod+srv,旧版本使用的mongodb,这里的选项,要使用旧版本,`1.11 or earlier`

步骤1获取到的是:MONGO_HOST

![image-20230725001040403](https://image.runtimes.cc/202404051405802.png)

查看Waline文档,查看MongoDB需要的环境配置

![image-20230724231654843](https://image.runtimes.cc/202404051405587.png)

具体的配置如下:

1. MONGO_HOST 就是从上面的步骤一 获取到的
2. 默认ssl=true
3. MONGO_AUTHSOURCE  从上面步骤4获取到的
4. MONGO_USER  前面创建的用户名
5. MONGO_PASSWORD 前面创建的密码
6. MONGO_PORT 设置默认值
7. MONGO_REPLICASET   如果是默认值的话,就是Cluster0,  自己修改的话,就用自己的
8. MONGO_DB = waline,   这个值也可以随便填, 不用手动创建库,waline会自动创建

![image-20230725001132730](https://image.runtimes.cc/202404051405827.png)

环境变量配置好之后,重新部署下就行了

![image-20230725001724373](https://image.runtimes.cc/202404051405198.png)

评论地址是:`vercel分配的域名`

后台系统是:`vercel分配的域名/ui`