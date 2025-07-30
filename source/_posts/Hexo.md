---
title: Hexo
categories:
  - 软件
tags:
  - Hexo
  - 内链
  - 相对路径
cover: https://image.runtimes.cc/202404051528435.png
abbrlink: 37733
date: 2022-12-04 01:26:00
updated: 2022-12-04 01:26:00
---

# 内链
## 内链图片
### 相对路径
在`source/`下创建一个图片的目录,比如起名叫`img`的文件夹
在文章中引入 `![图片的备注](图片的相对路径)`
比如`![测试](./2020.png)`

### 第二种相对路径
将`_config.yml`文件中的配置项`post_asset_folder`设为true后，执行命令
```shell
$ hexo new post_name
```
在source/_posts中会生成文章post_name.md和同名文件夹post_name。
将图片资源放在post_name中，文章就可以使用相对路径引用图片资源了。
```shell
![](image.jpg)
```

> 如果有遇到问题参考:[Hexo博客搭建之在文章中插入图片](https://yanyinhong.github.io/2017/05/02/How-to-insert-image-in-hexo-post/)

## 内链文章
### 相对路径
比如我的博客:
`[需要显示的文字](../310/#CloudFront加速Vercel)`
* `../` 相对路径,不是md文件的相对路径,是生成的public文件夹你的文章和内链文章的相对路径
* 310 是指`abbrlink: 310`,如果没有就使用标题
* `#CloudFront加速Vercel` 是指 310这篇文章的`#CloudFront加速Vercel`这个标题,也可以不用写,如果写了,就会自动跳到页面的这个锚点

