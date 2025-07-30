---
title: Element-UI
id: 35991801-6f73-4def-9cd0-c9d18e71cdd9
date: 2025-01-04 03:56:03
auther: connor
cover: null
excerpt: title Element-UI tags 前端 教程 笔记 Vue.js Element-UI categories 前端 Vue Element-UI cover https//image.runtimes.cc/202404051527979.jpg abbrlink 29696 
permalink: /archives/cb16ded0-da05-4a50-b629-425f3c0972bc
---

---
title: Element-UI
tags:
  - 前端
  - 教程
  - 笔记
  - Vue.js
  - Element-UI
categories:
  - 前端
  - Vue
  - Element-UI
cover: https://image.runtimes.cc/202404051527979.jpg
abbrlink: 29696
date: 2022-11-19 18:25:00
updated: 2023-03-20 18:25:00
---

# 表单验证
## 简单版
```html
<template>
  <div class="login">

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>通用后台管理系统</span>
      </div>

      <el-form
          label-width="80px"
          :model="form"
          ref="form">
        <el-form-item label="用户名" prop="username"
                      :rules="[
            {required:true,message:'请输入用户名',trigger:'blur'},
            {min:6,max:12,message:'长度在6-12位字符',trigger:'blur'},
        ]">
          <el-input v-model="form.username"></el-input>
        </el-form-item>

        <el-form-item label="密码" prop="password"
                      :rules="[
            {required:true,message:'请输入密码',trigger:'blur'},
            {min:6,max:12,message:'长度在6-12位字符',trigger:'blur'},
        ]">
          <el-input type="password" v-model="form.password"></el-input>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="login('form')">登录</el-button>
        </el-form-item>

      </el-form>

    </el-card>

  </div>
</template>

<script>
export default {
  data() {

    return {
      form: {
        username: "",
        password: ""
      }
    }

  },
  methods:{
    login(form){
      this.$refs[form].validate((valid)=>{
        if(valid){
          console.log("验证通过")
          this.axios.post("http://localhost",this.form).then(
              res=>{
                console.log(res)
                if(res.data.status === 200){
                  localStorage.setItem("username",res.data.username)
                  this.$message({
                    message:res.data.message,
                    type:'success'
                  })
                  this.$router.push('/home')
                }
              }
          )
              .catch(err=>{
                console.error(err)
              })
        }else {
          console.error("验证不通过")
        }
      })
    }
  }
}
</script>

<style scoped lang="scss">
.login {
  width: 100%;
  height: 100%;
  position: absolute;
  background: cadetblue;

  .box-card {
    width: 450px;
    margin: 200px auto;

    .el-button {
      width: 100%;
    }
  }
}

</style>
```

## 自定义校验
```html
<template>
  <div class="login">

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>通用后台管理系统</span>
      </div>

      <el-form
          label-width="80px"
          :model="form"
          :rules="rules"
          ref="form">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="form.username"></el-input>
        </el-form-item>

        <el-form-item label="密码" prop="password">
          <el-input type="password" v-model="form.password"></el-input>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="login('form')">登录</el-button>
        </el-form-item>

      </el-form>

    </el-card>

  </div>
</template>

<script>
export default {
  data() {

    // 验证用户名
    const validateName = (rule, value, callback) => {

      if (value === '') {
        callback(new Error("请输入用户名"))
      } else if (value === '123456') {
        console.log("11111111111")
        callback(new Error('换个用户名,猜到啦'))
      } else {
        callback()
      }
    }

    const validatePassword = (rule, value, callback) => {

      if (value === '') {
        callback(new Error("请输入密码"))
      } else if (!value === '123456') {
        callback(new Error('换个密码,猜到啦'))
      } else {
        callback()
      }
    }
    return {
      form: {
        username: "",
        password: ""
      },
      rules: {
        username: [{validator: validateName, trigger: 'blur'}],
        password: [{validator: validatePassword, trigger: 'blur'}],
      }
    }

  },
  methods: {
    login(form) {
      this.$refs[form].validate((valid) => {
        if (valid) {
          console.log("验证通过")
          // this.axios.post("http://localhost", this.form).then(
          //     res => {
          //       console.log(res)
          //       if (res.data.status === 200) {
          //         localStorage.setItem("username", res.data.username)
          //         this.$message({
          //           message: res.data.message,
          //           type: 'success'
          //         })
          //         this.$router.push('/home')
          //       }
          //     }
          // )
          //     .catch(err => {
          //       console.error(err)
          //     })
        } else {
          console.error("验证不通过")
        }
      })
    }
  }
}
</script>

<style scoped lang="scss">
.login {
  width: 100%;
  height: 100%;
  position: absolute;
  background: cadetblue;

  .box-card {
    width: 450px;
    margin: 200px auto;

    .el-button {
      width: 100%;
    }
  }
}

</style>
```

## 自定义校验封装
新建一个vaildate.js
```js
// 用户名匹配
export function nameRule(rule,value,callback){
    if (value === '') {
        callback(new Error("请输入用户名"))
    } else if (value === '123456') {
        console.log("11111111111")
        callback(new Error('换个用户名,猜到啦'))
    } else {
        callback()
    }
}

export function passwordRule(rule,value,callback){
    if (value === '') {
        callback(new Error("请输入密码"))
    } else if (value === '123456') {
        callback(new Error('换个密码,猜到啦'))
    } else {
        callback()
    }
}
```

```html
<template>
  <div class="login">

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>通用后台管理系统</span>
      </div>

      <el-form
          label-width="80px"
          :model="form"
          :rules="rules"
          ref="form">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="form.username"></el-input>
        </el-form-item>

        <el-form-item label="密码" prop="password">
          <el-input type="password" v-model="form.password"></el-input>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="login('form')">登录</el-button>
        </el-form-item>

      </el-form>

    </el-card>

  </div>
</template>

<script>
import {nameRule,passwordRule} from "@/utils/vaildate";

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
          // this.axios.post("http://localhost", this.form).then(
          //     res => {
          //       console.log(res)
          //       if (res.data.status === 200) {
          //         localStorage.setItem("username", res.data.username)
          //         this.$message({
          //           message: res.data.message,
          //           type: 'success'
          //         })
          //         this.$router.push('/home')
          //       }
          //     }
          // )
          //     .catch(err => {
          //       console.error(err)
          //     })
        } else {
          console.error("验证不通过")
        }
      })
    }
  }
}
</script>

<style scoped lang="scss">
.login {
  width: 100%;
  height: 100%;
  position: absolute;
  background: cadetblue;

  .box-card {
    width: 450px;
    margin: 200px auto;

    .el-button {
      width: 100%;
    }
  }
}

</style>
```

# 遍历路由渲染菜单栏
src>common>menu.vue
```html
<template>

  <div class="menu">

    <el-aside width="200px">

      <el-menu
          router
          default-active="2"
          class="el-menu-vertical-demo"
          background-color="cornflowerblue"
          text-color="#fff"
          active-text-color="#ffd04b">
        <template v-for="(item,index) in menus">
          <div>
            <el-submenu :index="index + '' " :key="index" v-if="!item.hidden">
              <template slot="title">
                <i :class="item.iconClass"></i>
                <span>{{ item.name }}</span>
              </template>
              <el-menu-item-group v-for="(child,index) in item.children" :key="index">
                <el-menu-item :index="child.path">
                  <i :class="child.iconClass"></i>
                  {{ child.name }}
                </el-menu-item>
              </el-menu-item-group>
            </el-submenu>
          </div>
        </template>

      </el-menu>
    </el-aside>
  </div>
</template>

<script>
export default {
  data(){
    return{
      menus:[]
    }
  },
  created() {
    console.log(this.$router.options.routes)
    this.menus=this.$router.options.routes
  },
  methods: {
  }
}
</script>

<style scoped lang="scss">

.menu{
  .el-aside{
    height: 100%;
    .el-menu{
      height: 100%;
      .fa{
        margin-right: 10px;
      }
    }

    .el-submenu .el-menu-item{
      min-width: 0;
    }
  }
}
</style>
```
route.js
```js
import Vue from 'vue'
import Router from 'vue-router'
// import  Home from '../components/Home.vue'

Vue.use(Router)

export default new Router({
    routes:[
        {
            path:'/',
            // 重定向
            redirect:'/login',
            // 路由懒加载
            hidden:true,
            component:()=>import('@/components/Login.vue')
            // 异步组件
        },
        {
            path:'/login',
            name:'Login',
            hidden:true,
            // 路由懒加载
            component:()=>import('@/components/Login.vue')
            // 异步组件
        },
        {
            path:'/home',
            // component:Home
            hidden:true,
            // 路由懒加载
            component:()=>import('@/components/Home.vue')
            // 异步组件
        },
        {
            path:'/home2',
            // component:Home
            hidden:true,
            // 路由懒加载
            component:resolve =>require(['@/components/Home.vue'],resolve)
            // 异步组件
        },
        {
            path:'*',
            hidden:true,
            // 路由懒加载
            component:()=>import('@/components/404.vue')
        },
        {
            path:"/home",
            name:"学生管理",
            iconClass:'fa fa-user',
            // 默认重定向
            redirect:'/home/student',
            component:()=>import('@/components/Home.vue'),
            children:[
                {
                    path:'/home/student',
                    name:'学生列表',
                    iconClass:"fa fa-list",
                    component:()=>import("@/components/students/StudentList.vue")
                },
                {
                    path:'/home/info',
                    name:'信息列表',
                    iconClass:"fa fa-list",
                    component:()=>import("@/components/students/InfoList.vue")
                },
                {
                    path:'/home/infos',
                    name:'信息管理',
                    iconClass:"fa fa-list-alt",
                    component:()=>import("@/components/students/InfoLists.vue")
                },
                {
                    path:'/home/work',
                    name:'作业列表',
                    iconClass:"fa fa-list-ul",
                    component:()=>import("@/components/students/WorkList.vue")
                },
                {
                    path:'/home/workd',
                    name:'作业管理',
                    iconClass:"fa fa-th-list",
                    component:()=>import("@/components/students/WorkMent.vue")
                }
            ]
        },
        {
            path:"/home",
            name:"数据分析",
            iconClass:'fa fa-bar-chart',
            component:()=>import('@/components/Home.vue'),
            children:[
                {
                    path:'/home/dataview',
                    name:'数据概览',
                    iconClass:"fa fa-list-alt",
                    component:()=>import("@/components/dataAnalysis/DataView.vue")
                },
                {
                    path:'/home/mapview',
                    name:'地图概览',
                    iconClass:"fa fa-list-alt",
                    component:()=>import("@/components/dataAnalysis/MapView.vue")
                },
                {
                    path:'/home/travel',
                    name:'信息管理',
                    iconClass:"fa fa-list-alt",
                    component:()=>import("@/components/dataAnalysis/TraveMap.vue")
                },
                {
                    path:'/home/score',
                    name:'分数地图',
                    iconClass:"fa fa-list-alt",
                    component:()=>import("@/components/dataAnalysis/ScoreMap.vue")
                }
            ]
        },
        {
            path:"/users",
            name:"用户中心",
            iconClass:'fa fa-user',
            component:()=>import('@/components/Home.vue'),
            children:[
                {
                    path:'/users/user',
                    name:'权限管理',
                    iconClass:"fa fa-user",
                    component:()=>import("@/components/users/User.vue")
                }
            ]
        },
    ],
    mode:'history'
})
```

# 面包屑的使用
不同的路由,面包屑显示的信息会自动变化
bread.vue
```html
<template>
  <div>
    <el-card>
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item
            v-for="(item,index) in $route.matched"
            :key="index"
        >{{item.name}}</el-breadcrumb-item>
      </el-breadcrumb>
    </el-card>
  </div>
</template>

<script>
export default {
}
</script>

<style scoped>

</style>
```
home.vue
```html
<template>
  <div class="home">
    <Header></Header>
    <el-container class="content">
      <Menu>menu</Menu>

      <el-container>

        <el-main>
          <Breadcrumb/>
          <div class="cont">
            <router-view></router-view>
          </div>
        </el-main>
        <el-footer>
          <Footer></Footer>
        </el-footer>
      </el-container>
    </el-container>

  </div>
</template>

<script>
import Header from "@/components/common/Header.vue";
import Footer from "@/components/common/Footer.vue";
import Menu from "@/components/common/Menu.vue";
import Breadcrumb from '@/components/common/Breadcrumb.vue'

export default {
  components: {
    Header,
    Footer,
    Breadcrumb,
    Menu,
  },
  data() {
    return {}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.home {
  width: 100%;
  height: 100%;
  .content{
    position: absolute;
    width: 100%;
    top: 60px;
    bottom: 0;
    .cont{
      margin: 20px 0;
    }
  }
}

</style>
```

# Card 卡片
```html
<!--    body-style的使用        -->
<el-card :body-style="{padding:'0px'}">  
  <div style="padding-top: 4px;display: flex">  
  </div
</div></el-card>
```

# 级联选择器

新版本props的使用级联选择器太高可以在全局样式里给.el-cascader-panel设置高度为200px:props="{ expandTrigger: 'hover', value: 'cat_id', label: 'cat_name', children: 'children' }"

多选框

```
<div>
    <el-checkbox-group v-model="checkboxGroup1">
        <el-checkbox-button v-for="city in cities" :label="city" :key="city">{{city}}</el-checkbox-button>
    </el-checkbox-group>
</div>

const cityOptions = ['上海', '北京', '广州', '深圳'];
  export default {
    data () {
      return {
        checkboxGroup1: ['上海'],
        checkboxGroup2: ['上海'],
        checkboxGroup3: ['上海'],
        checkboxGroup4: ['上海'],
        cities: cityOptions
      };
    }
```

# Avatar 头像
```html
<el-avatar shape="square" size="small" src="https://baidu.com/logo"></el-avatar>

```

# Table 表格
## el-table怎样隐藏某一列
el-table-column上添加`v-if="false"`
```vue
    <el-table v-loading="loading" :data="bczglList" @selection-change="handleSelectionChange">
      <el-table-column label="id" align="center" prop="id" v-if="false" />
      <el-table-column label="班次组编号" align="center" prop="bczbh" />
      <el-table-column label="操作" align="center" class-name="small-padding fixed-width">
        <template slot-scope="scope">
          <el-button
            size="mini"
            type="text"
            icon="el-icon-edit"
            @click="handleUpdate(scope.row)"
            v-hasPermi="['kqgl:bczgl:edit']"
          >修改</el-button>
          <el-button
            size="mini"
            type="text"
            icon="el-icon-delete"
            @click="handleDelete(scope.row)"
            v-hasPermi="['kqgl:bczgl:remove']"
          >删除</el-button>
        </template>
      </el-table-column>
    </el-table>
 
```

# 先记着

Vue同一个dom元素绑定多个点击事件如何绑定

```
<el-button @click="dialogVisible = false;clearTempData()">取 消</el-button>
```

模板获取值

```
<template slot-scope="scope">
    <el-tag type="warning" v-for="ids in scope.row.typeID.split(',')">{{getGameType(ids)}}</el-tag>
</template>
```

Store 数据

```
agentId: _this.$store.getters.name.agentId,
```