---
title: NodeJs
tags:
  - NodeJs
  - 前端
categories:
  - 前端
  - NodeJs
cover: https://image.runtimes.cc/202404051531669.png
abbrlink: 19874
date: 2022-11-19 18:25:00
updated: 2024-03-19 20:45:19
---

# 语法





# 基本使用

初始化项目

```bash
npm init -y 
```

创建文件

```bash
# 新建src文件夹
# 在src里新建index.html和index.css
```

安装jQuery

```bash
# -S 的意思是 jquery安装好了,记录在package.json的 dependencies里
# -S 等于 --save的简写
npm i jquery -S
```

index.html

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>隔行变色</title>
				<script src="./index.js"></script>
    </head>
    <body>
        <ul>
            <li>这是第 1 个li</li>
            <li>这是第 2 个li</li>
            <li>这是第 3 个li</li>
            <li>这是第 4 个li</li>
            <li>这是第 5 个li</li>
            <li>这是第 6 个li</li>
            <li>这是第 7 个li</li>
            <li>这是第 8 个li</li>
            <li>这是第 9 个li</li>
        </ul>

    </body>
</html>
```

index.js

```jsx
// 使用ES6的导入语法,导入jQuery
import $ from 'jquery'

$(function(){
    $("li:odd").css("background-color","red")
    $("li:even").css("background-color","pink")
})
```

运行index.html,就会报错,这时需要安装webpack

安装webpack

⛔ 安装webpack遇到的版本问题

要安装最新版本或特定版本，请运行以下命令之一：

```
npm install --save-dev webpack
npm install --save-dev webpack@<version>

```

如果你使用 webpack 4+ 版本，你还需要安装 CLI。

```
npm install --save-dev webpack-cli
```

</aside>

```bash
# 这个时候运行就会出问题
# 需要安装webpack
# @是指定版本
# -D是指把版本信息记录到 devDepencies
# devDepencies开发阶段会用到
# -D  等于 --save-dev的简写
npm insatll webpack@5.42.1  webpack-cli@4.7.2 -D
```

创建webpack的配置文件webpack.config.js

```bash
# 项目跟目录创建webpack配置文件,webpack.config.js
# 使用Node.js的导出语法,向外导出一个webpack的配置对象
modle.exports={

  // 代表webpack的运行的模式,
	// 可选的值有两个 development 和 production
	mode : "development"
}

```

修改package.json

```bash
# 在package.json的scripts节点下,新增dev脚本
"scripts":{
	"dev":"webpack"
}
```

运行

```bash
# 就会多了个dist的文件夹
npm run dev
```

修改index.html的引入js路径

```bash
# 修改index.html文件夹的scr导入路劲
scr="./index.js" 改成 src="./dist/main.js"
```

# entry和output

```jsx
const path = require("path")
module.exports = {
    // 指定要最先处理的文件
    entry:path.join(__dirname,"./src/index1.js"),
    // 指定生成的文件放在哪里
    output:{
        // 生成文件的目录
        path:path.join(__dirname,"./dist2"),
        // 生成的文件名
        filename:"bundle.js"
    }
}
```

# 修改代码实时生效插件

```bash
npm install webpack-dev-server --save-dev
```

修改package.json

```json
"scripts": {
    "dev": "webpack serve"
}
```

```bash
npm i --save-dev html-webpack-plugin
```

修改webpack.config.json

```jsx
const htmlplugin = require("html-webpack-plugin")

plugin = new htmlplugin({
    // 指定要服务的页面
    template:"./src/index.html",
    // 复制到哪里去
    filename:"./index.html"
})

module.exports = {
    mode:"development",
    plugins:[plugin]
}
```

# 配置devServer

```jsx
module.exports = {
    mode:"development",
    plugins:[plugin],
    devServer:{
        // 自动打开浏览器
        open:true,
        // 端口
        port:10086
    }
}
```

# loader

webpack默认只处理js格式的文件,比如要处理CSS,需要安装

```bash
npm install --save-dev css-loader
npm install --save-dev style-loader
```

在src下新建css文件夹,在css文件夹下新建一个index.css

在index.js导入css文件

```jsx
// 导入样式,在webpack中,所有的文件都可以用ES6语法导入
import "./css/index.css"
```

配置webpack.config.js

```jsx
// 先找到css-loader处理,再给style-loader处理
module.exports = {
    mode:"development",
    module:{
        // 所有第三方文件模块的匹配规则
        rules:[
            // 文件后缀的匹配规则
            {
                test:/\.css$/,use:["style-loader","css-loader"]
            }
        ]
    }
}
```

## less-loader

在上面的css文件夹下,创建个index.less文件

```less
// index.less
html,body,ul{
  padding: 0;
  margin: 0;
  li{
    line-height: 30px;
    padding-left: 20px;
    font-size: 12px;
  }
}
```

```bash
npm install less -D
npm install less less-loader --save-dev
```

配置webpack.config.js

```less
module.exports = {
    mode:"development",
    module:{
        // 所有第三方文件模块的匹配规则
        rules:[
            // 文件后缀的匹配规则
            // 处理css
            {test:/\.css$/,use:["style-loader","css-loader"]},
            // 处理less
            {test: /\.less$/, use: ["style-loader", "css-loader", "less-loader",],
            },
        ]
    }
}
```

## 图片loader

没学

## babel-loader

没学

# build

## 打包优化

## 自动清理dist目录下的旧文件

```bash
npm install --save-dev clean-webpack-plugin
```

```jsx
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
    plugins:[plugin,new CleanWebpackPlugin()],
    output:{
        path:path.join(__dirname,"./dist")
    }
}
```

# Source Map

一个信息文件,存储这位置信息,存储压缩混淆后的代码,所对应的转换钱的位置

```jsx
module.exports = {
    mode:"development",
		// 生产的时候要关闭,也可以 设置 nosources-source-map
		// nosources-source-map 显示行号,不显示源码
    devtool:"eval-source-map"
}
```

# `@`的原理

标识src源代码目录,从外往里找, 就可以不用使用`../`从里往外找

告诉webpack,@的目录

```jsx
module.exports = {
    resolve:{
        alias:{
            "@":path.join(__dirname,"./src/")
        }
    }
}
```
# Eslint
```json
{
	// 是否使用分号结尾
	"semi": true,
	
	// 是否使用单引号
	"singleQuote": false,
	
	// 最后一句话逗号结尾
	"trailingComma": "none"
}
```
在Vue项目的根目录中创建vue.config.js文件
```json
# 在脚手架生成的代码添加
module.exports = defineConfig({
	# 要添加的代码
  lintOnSave:false
})

# 或者
module.exports = {
  lintOnSave:false
}
```

# 入门应用

```js
var http = require('http');

http.createServer(function (request, response) {
    // 发送 HTTP 头部
    // HTTP 状态值: 200 : OK
    // 内容类型: text/plain
    response.writeHead(200, {'Content-Type': 'text/plain'});

    // 发送响应数据 "Hello World"
    response.end('Hello World\n');
}).listen(8888);

// 终端打印如下信息
console.log('Server running at http://127.0.0.1:8888/');

// 运行
node server.js
```

Npm升级

```bash
npm i -g npm to update
```

# Node.js

mac安装

```shell
brew install nodejs
```

npm更换版本

```shell
# npm更换
npm install npm@6.10.1 -g
```

node.js更换版本

```shell
# node更换
sudo npm install n -g

# 安装指定版本号的nodejs,记得使用sudo
sudo n 14.16.0

# 切换nodejs版本号
n
# 输入n之后,打印的数据
ο node/14.16.0

------------------------------------------
# 使用nvm更换版本
brew install nvm
# 查看远程版本
nvm ls-remote
# 安装制定版本号
nvm install v15.3.2
# 已经安装的版本号
nvm ls
# 切换已经安装的版本号
nvm use 4.2
```

依赖管理

```shell
# 安装但不写入package.json；
npm install xxx

# 安装并写入package.json的"dependencies"中
npm install xxx –S

# 安装并写入package.json的"devDependencies"中
npm install xxx –D

# 全局安装
npm install xxx -g

# 安装指定版本
npm install xxx@1.2.0

# 更新包
npm install -g npm-check-updates
# 检查可更新的模块
ncu
# 来更新package.json的依赖包到最新版本
ncu -u
```

node.js升级

```shell
# 更新npm
npm install -g npm

# 清空npm缓存
npm cache clean -f

# 安装n模块
npm install -g n

# 升级node.js到最新稳定版
n stable

# 有时候需要sudo权限,或者安装好了之后要开启一个新的shell才能用到新的版本
```

[https://www.bilibili.com/video/BV1vE411871g?p=120](https://www.bilibili.com/video/BV1vE411871g?p=120)

[安装源](https://github.com/nodesource/distributions#debinstall)

依赖升级

```bash
npm install -g npm-upgrade # 先全局安装 npm-upgrade
npm-upgrade                # 当前项目下的包全都更新
npm-upgrade <package>      # 当前项目下的指定包更新
```

# ES6 & JS

[字符串转数字](https://blog.fundebug.com/2018/07/07/string-to-number/)

[遍历2](https://segmentfault.com/a/1190000010203337)

[排序](https://segmentfault.com/a/1190000015961859)

[排序](https://blog.csdn.net/weixin_46146313/article/details/104198636)

[join方法](https://www.w3school.com.cn/jsref/jsref_join.asp)

遍历

```
  JavaScript for...in ..
```

foreach

```
row.typeID.split(",").forEach(id=>{this.gameTypes.push(parseInt(id))})
```

# NodJs项目部署到Vercel

1.创建node项目

```shell
npm init
```

2.安装依赖,package.json

```json
{
  "dependencies": {
    "@vercel/node": "^2.15.3",
    "express": "^4.18.2"
  }
}
```

3.创建vercel.json

```json
{
  "version": 2,
  "builds": [
    {
      "src": "app.js",
      "use": "@vercel/node"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "app.js"
    }
  ]
}
```

4.创建app.js

```js
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
    res.send('Hello World!')
})

app.listen(port, () => {
    console.log(`Example app listening on port ${port}`)
})
```

5.像平常部署别的项目一样,部署这个项目就可以,部署好之后,就能看到首页的`Hello World!`

