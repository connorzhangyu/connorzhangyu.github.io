---
title: Vue后台系统
tags:
  - 前端
  - 教程
  - 笔记
  - Vue.js
categories:
  - 前端
  - Vue
cover: https://image.runtimes.cc/202404051535734.jpg
abbrlink: 64129
date: 2024-02-26 13:47:19
updated: 2024-02-26 13:47:21
---
项目由 Vue3 + Pina + JavaScript  + Ant Design Vue

# 创建项目

```shell
# 直接创建
npm create vite@latest my-vue-app -- --template vue

# 如果要创建ts或者自定义配置的项目就用这个
npm create vite
```

# 引入Ant Design Vue

```shell
npm i --save ant-design-vue@4.x
```



mian.js

删掉自带的style.css文件

```javascript
import {createApp} from 'vue'
import Antd from 'ant-design-vue';
import 'ant-design-vue/dist/reset.css';
import App from './App.vue'
const app = createApp(App);

app.use(Antd)
app.mount('#app');
```

# 安装router

```shell
npm install vue-router@4
```

创建src/router/index.js文件

```javascript
import { createWebHistory, createRouter } from 'vue-router'

/**
 * Note: 路由配置项
 *
 * hidden: true                     // 当设置 true 的时候该路由不会再侧边栏出现 如401，login等页面，或者如一些编辑页面/edit/1
 * alwaysShow: true                 // 当你一个路由下面的 children 声明的路由大于1个时，自动会变成嵌套的模式--如组件页面
 *                                  // 只有一个时，会将那个子路由当做根路由显示在侧边栏--如引导页面
 *                                  // 若你想不管路由下面的 children 声明的个数都显示你的根路由
 *                                  // 你可以设置 alwaysShow: true，这样它就会忽略之前定义的规则，一直显示根路由
 * redirect: noRedirect             // 当设置 noRedirect 的时候该路由在面包屑导航中不可被点击
 * name:'router-name'               // 设定路由的名字，一定要填写不然使用<keep-alive>时会出现各种问题
 * query: '{"id": 1, "name": "ry"}' // 访问路由的默认传递参数
 * roles: ['admin', 'common']       // 访问路由的角色权限
 * permissions: ['a:a:a', 'b:b:b']  // 访问路由的菜单权限
 * meta : {
    noCache: true                   // 如果设置为true，则不会被 <keep-alive> 缓存(默认 false)
    title: 'title'                  // 设置该路由在侧边栏和面包屑中展示的名字
    icon: 'svg-name'                // 设置该路由的图标，对应路径src/assets/icons/svg
    breadcrumb: false               // 如果设置为false，则不会在breadcrumb面包屑中显示
    activeMenu: '/system/user'      // 当路由设置了该属性，则会高亮相对应的侧边栏。
  }
 */

// 公共路由
export const constantRoutes = [
  {
    path: '/login',
    component: () => import('@/views/login'),
    hidden: true
  },
]

const router = createRouter({
  history: createWebHistory(),
  routes: constantRoutes,
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition
    } else {
      return { top: 0 }
    }
  },
});

export default router;
```

# 配置开发环境

编辑vite.config.js

```javascript
import { defineConfig, loadEnv } from 'vite'
import path from 'path'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig(({ mode, command }) => {
  return {
    plugins: [vue()],
    resolve: {
      // https://cn.vitejs.dev/config/#resolve-alias
      alias: {
        // 设置路径
        '~': path.resolve(__dirname, './'),
        // 设置别名
        '@': path.resolve(__dirname, './src')
      },
      // https://cn.vitejs.dev/config/#resolve-extensions
      extensions: ['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json', '.vue']
    }
  }
})
```

项目根目录创建环境配置文件

.env.development

.env.prod

```
# 页面标题
VITE_APP_TITLE = 若依管理系统

# 开发环境配置
VITE_APP_ENV = 'development'

# 若依管理系统/开发环境
VITE_APP_BASE_API = '/dev-api'
```

# 安装sass

```shell
npm install -D sass

# 直接使用就可以
<style lang="scss" scoped>
</style>
```

# 全局样式

创建src/style/index.scss文件

```scss
@import './demo.scss';
html {
  height: 100vh;
  box-sizing: border-box;
}

body {
  height: 100vh;
  margin: 0;
  //-moz-osx-font-smoothing: grayscale;
  //-webkit-font-smoothing: antialiased;
  //text-rendering: optimizeLegibility;
  //font-family: Helvetica Neue, Helvetica, PingFang SC, Hiragino Sans GB, Microsoft YaHei, Arial, sans-serif;
}

#app {
  height: 100vh;
}
```

创建src/style/demo.scss文件

```scss
// 这个文件是用来写css的,前面导入的这个文件,所以后面只需要写就行了
```

导入scss,编辑main.js

```javascript
import './style/index.scss'
```

# Ant Design Vue Icon的使用

```vue
<template>
  <a-form class="login-from">
    <a-form-item size>
      <a-input placeholder="请输入用户名" size="large">
        <template #prefix>
          <UserOutlined/>
        </template>
      </a-input>
    </a-form-item>
  </a-form>
</template>

<script setup>
  import {UserOutlined, LockOutlined} from '@ant-design/icons-vue'
</script>
```

# 安装Axios

```shell
npm install axios
```

创建 src/utils/request.js

```javascript
import axios from 'axios'

axios.defaults.headers['Content-Type'] = 'application/json;charset=utf-8'
// 创建axios实例
const service = axios.create({
  // axios中请求配置有baseURL选项，表示请求URL公共部分
  baseURL: import.meta.env.VITE_APP_BASE_API,
  // 超时
  timeout: 10000
})

// request拦截器
service.interceptors.request.use(config => {

  return config
}, error => {
    console.log(error)
    Promise.reject(error)
})

// 响应拦截器
service.interceptors.response.use(res => {
        // 未设置状态码则默认成功状态
    const code = res.data.code || 200;
    if (code === 401) {

      return Promise.reject('无效的会话，或者会话已过期，请重新登录。')
    } else if (code === 500) {
      ElMessage({ message: msg, type: 'error' })
      return Promise.reject(new Error(msg))
    } else if (code === 601) {
      ElMessage({ message: msg, type: 'warning' })
      return Promise.reject(new Error(msg))
    } else if (code !== 200) {
      ElNotification.error({ title: msg })
      return Promise.reject('error')
    } else {
      return  Promise.resolve(res.data)
    }
  },
  error => {
    console.log('err' + error)
    let { message } = error;
    if (message == "Network Error") {
      message = "后端接口连接异常";
    } else if (message.includes("timeout")) {
      message = "系统接口请求超时";
    } else if (message.includes("Request failed with status code")) {
      message = "系统接口" + message.substr(message.length - 3) + "异常";
    }
    ElMessage({ message: message, type: 'error', duration: 5 * 1000 })
    return Promise.reject(error)
  }
)

export default service
```

main.js

```javascript
import router from './router'
```



