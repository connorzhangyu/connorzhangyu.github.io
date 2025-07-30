---
title: DataGrip
tags:
  - Mac
categories:
  - 软件
cover: https://image.runtimes.cc/202404051526687.webp
abbrlink: 25177
date: 2022-11-19 18:25:00
updated: 2023-12-01 18:40:21
---

# 查看ddl和复制为insert语句

![](https://image.runtimes.cc/202404051411320.png)

# 显示

## 设置数据库颜色

![image-20230723151325737](https://image.runtimes.cc/202404051411473.png)

## 显示树状结构

![image-20230726133150472](https://image.runtimes.cc/202404051412133.png)

## 显示表的commnet

![image-20230726133353607](https://image.runtimes.cc/202404051412038.png)

## 显示数据库DDL最后一次更新的时间

![image-20230726133451693](https://image.runtimes.cc/202404051412291.png)

# 同步/比较两个表的数据结构

用command 选中 两个想要比较的表,然后在其中一个表,选择

![image-20230723151708711](https://image.runtimes.cc/202404051412792.png)

只显示不一样的字段
![image-20230723151821734](https://image.runtimes.cc/202404051412925.png)

显示全部字段,绿色表示不一样

![image-20230723151913910](https://image.runtimes.cc/202404051412733.png)

也可以比较两个库,还是刚才的方法,选中两个库,再比较

![image-20230723153819424](https://image.runtimes.cc/202404051412216.png)

相当于Navicat的这个功能

![image-20230723153427586](https://image.runtimes.cc/202404051413066.png)

# 复制功能

标记1,选中表,`Command + c`  就是复制的表名

标记2,选中库,`Command + c`  就是复制的库名

![image-20230726133739963](https://image.runtimes.cc/202404051414322.png)

右边的选项,让你复制出的SQL格式是什么样的,比如复制出insert的sql,或者update的sql,或者导出xml 或者,多条记录复制出一条sql语句,简单使用可以尝试

build-in:
* sql inserts
* sql updates

scripted:
* sql-insert-multirow
* Sql-insert-statements
* 
![image-20230726133946342](https://image.runtimes.cc/202404051414566.png)



# 导出的功能

使用mysqldump,导出整个库

![image-20230726134405227](https://image.runtimes.cc/202404051414556.png)

![image-20230726134504395](https://image.runtimes.cc/202404051414094.png)

Mac电脑需要安装,`mysql-client`

```shell
brew install mysql-client

# 二进制目录(需要手动复制到上图的路径):
/opt/homebrew/opt/mysql-client/bin/mysqldump
```

# 导入SQL

要在库名上点右键

![image-20231201154603739](https://image.runtimes.cc/202404051414735.png)

这个功能类似于

<img src="https://image.runtimes.cc/202404051414049.png" alt="image-20231201154745846" style="zoom:50%;" />

