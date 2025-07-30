---
title: Vue项目
tags:
  - 前端
  - 教程
  - 笔记
  - Vue.js
categories:
  - 前端
  - Vue
cover: https://image.runtimes.cc/202404051535734.jpg
abbrlink: 31217
date: 2022-11-19 18:25:00
updated: 2025-05-23 23:31:57
---

# 安装Vue
## 局部安装
局部安装并使用vue-cli 4.x版本,和创建项目
```shell
# 先创建一个文件夹
# 初始化一个node项目
npm init -y

# 局部安装vue
npm i -D @vue/cli

# 查看vue的版本
# 局部安装要用npx命令
C:\Users\mmzcg\WebstormProjects>npx vue -V
@vue/cli 5.0.8

# 创建一个项目
# 为啥不能用npm运行
npx vue create project-one

# 进入项目并且运行
cd project-one
npm run serve

# 也可以用ui
npx vue ui
```
## 全局安装
{% tabs Vue %}
<!-- tab Vue2-->
```shell
npm install -g @vue/cli

vue create my-project

vue ui
```
<!-- endtab -->

<!-- tab Vue3-->

[Vue脚手架官网](https://cn.vuejs.org/guide/quick-start.html)

```shell
# 安装脚手架
# 会有选项
pnpm create vue@latest

# 进入项目
cd vue-project

# 安装依赖
pnpm install

# 运行项目
pnpm dev
```
<!-- endtab -->
{% endtabs %}

# 安装Element-UI
## 全局安装
```shell
# 安装
# npm
npm i element-ui -S

# pnpm
pnpm add element-ui -S
```
```js
// main.js 使用
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(ElementUI)
```

## 按需引入
```shell
# 安装
npm i element-ui -S
npm install babel-plugin-component -D
```

```js
// main.js
// 全局引入
// import ElementUI from 'element-ui'
// import 'element-ui/lib/theme-chalk/index.css'
// Vue.use(ElementUI)

// 按需引入
import {Button} from 'element-ui'
Vue.use(Button)

// babel.config.js
module.exports = {
	// 自动生成的
  presets: [
    '@vue/cli-plugin-babel/preset'
  ],
	// 从官网上复制的
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
```
如果没有找到.babelirc文件,找找babel.config.js文件
![](https://image.runtimes.cc/202404051519062.png)

一个一个import 按需引入有点麻烦,在根目录下面创建一个plugins文件夹,在这个文件夹下面创建element.js
```js
import Vue from 'vue'
import {Button} from 'element-ui'
Vue.use(Button)
```
在main.js引入element.js
```js
import '../element.js'
```

# 安装CSS预处理器
## Sass

安装的是sass,使用的时候是scss.... 留意

```shell
npm install --save-dev node-sass
npm install --save-dev sass-loader
```

```js
# 注意这里是scss
<style scoped lang="scss">
.hello{
  background: yellow;
  .el-button{
    color: red
  }
}
```

## Less
```shell
npm i less less-loader --save-dev
npm i less less-loader --save-dev
```

```js
<style scoped lang="less">
.hello{
  background: yellow;
  .el-button{
    color: red
  }
}
```

# 重置样式
下面的代码可以搜索`reset.css`
在src/assets 下创建css文件夹,在这个文件下创建reset.css文件,粘贴
```css
/* http://meyerweb.com/eric/tools/css/reset/
   v2.0 | 20110126
   License: none (public domain)
*/

html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed,
figure, figcaption, footer, header, hgroup,
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
    margin: 0;
    padding: 0;
    border: 0;
    font-size: 100%;
    font: inherit;
    vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure,
footer, header, hgroup, menu, nav, section {
    display: block;
}
body {
    line-height: 1;
}
ol, ul {
    list-style: none;
}
blockquote, q {
    quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
    content: '';
    content: none;
}
table {
    border-collapse: collapse;
    border-spacing: 0;
}
```
在App.vue或者其他的组件中使用

```js
<style lang="scss">
@import url('./assets/css/reset.css');
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

# 安装图标库
```shell
# 安装
npm i -D font-awesome

main.js
import 'font-awesome/css/font-awesome.min.css'

# 使用
<i class="fa fa-user"></i>
```

#  安装axios
```shell
npm i axios -S

main.js
import axios from "axios";
# 挂在之后,就可以全局使用了
Vue.prototype.axios = axios
```

# 安装vue-router
```shell
# vue-router3 和 4 的配置不一样
npm install vue-router@3.5.3
```

创建文件和文件夹
src->router->index.js
```js
import Vue from 'vue'
import Router from 'vue-router'
import  Home from '../components/Home.vue'

Vue.use(Router)

export default new Router({
    routes:[
        {
            path:'/home',
            component:Home
        }
    ],
    mode:'history'
})

main.js
import router from './router'

new Vue({
  render: h => h(App),
  router,
}).$mount('#app')
```

# 路由懒加载和异步组件
```js
export default new Router({
    routes:[
        {
            path:'/home',
            // 路由懒加载
            component:()=>import('@/components/Home.vue')
        },
        {
            path:'/home2',
						// 异步组件
            component:resolve =>require(['@/components/Home.vue'],resolve)
        }
    ],
    mode:'history'
})
```

# setToken封装
src→utils→settoken.js
```js
export function setToken(key,token){
    return localStorage.setItem(key,token)
}

export function getToken(key){
    return localStorage.getItem(key)
}

export function delToken(key){
    return localStorage.removeItem(key)
}
```

使用
```js
<script>
import {nameRule,passwordRule} from "@/utils/vaildate";
import {setToken} from "@/utils/setToken";

export default {
  data() {
    return {
      form: {
        username: "",
        password: ""
      },
      rules: {
        username: [{validator: nameRule, trigger: 'blur'}],
        password: [{validator: passwordRule, trigger: 'blur'}],
      }
    }

  },
  methods: {
    login(form) {
      this.$refs[form].validate((valid) => {
        if (valid) {
          console.log("验证通过")
          this.axios.post("http://localhost", this.form).then(
              res => {
                console.log(res)
                if (res.data.status === 200) {
                  setToken("username", res.data.username)
                  this.$message({
                    message: res.data.message,
                    type: 'success'
                  })
                  this.$router.push('/home')
                }
              }
          )
              .catch(err => {
                console.error(err)
              })
        } else {
          console.error("验证不通过")
        }
      })
    }
  }
}
</script>
```

# 配置代理服务器
```js
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,

  lintOnSave: false,
  devServer:{
    // 运行自动打开浏览器,
    open:true,
    // 如果不配置这个,自动打开浏览器,就是0.0.0.0:8080
    host:"localhost",
    proxy:{
      '/api':{
        target:'http://127.0.0.1',
        changeOrigin:true,
        pathRewrite:{
          '^/api':''
        }
      }
    }
  }
})
```

# 404
```js
{
    path:'*',
    // 路由懒加载
    component:()=>import('@/components/404.vue')
}
```