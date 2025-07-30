---
title: vue
id: b44ae8de-f8e3-49f2-b7f1-3b2c742ae207
date: 2025-01-04 03:56:05
auther: connor
cover: null
excerpt: title Vue tags 前端 教程 笔记 Vue.js categories 前端 Vue cover https//image.runtimes.cc/202404051535734.jpg abbrlink 43782 date 2022-11-19 182500 upd
permalink: /archives/fc9356aa-4f47-40b9-a38c-a6a79d123714
---

---
title: Vue
tags:
  - 前端
  - 教程
  - 笔记
  - Vue.js
categories:
  - 前端
  - Vue
cover: https://image.runtimes.cc/202404051535734.jpg
abbrlink: 43782
date: 2022-11-19 18:25:00
updated: 2024-07-06 16:58:18
---

按照官方文档的教程步骤来,大部分的代码Demo,都是通过脚手架创建的项目的基础上创建的,用的版本的Vue3,Typescript,组合式的写法

# 基础

## 创建一个应用

***多个应用实例***

没什么用,现在项目都是从头开始都是vue项目,不会用来一步一步替换,没有想到应用场景

```html
<!DOCTYPE html>
<html>
<head>
  <title>Vue Multiple Instances Example</title>
  <script src="https://cdn.jsdelivr.net/npm/vue@2"></script>
</head>
<body>
  <div id="app1">
    <p>{{ message }}</p>
    <button @click="updateMessage">更新消息</button>
  </div>

  <div id="app2">
    <p>{{ message }}</p>
    <button @click="updateMessage">更新消息</button>
  </div>

  <script>
    var app1 = new Vue({
      el: '#app1',
      data: {
        message: '这是第一个 Vue 实例的消息'
      },
      methods: {
        updateMessage() {
          this.message = '第一个实例的消息已更新';
        }
      }
    });

    var app2 = new Vue({
      el: '#app2',
      data: {
        message: '这是第二个 Vue 实例的消息'
      },
      methods: {
        updateMessage() {
          this.message = '第二个实例的消息已更新';
        }
      }
    });
  </script>
</body>
</html>
```

## 模板语法

***文本插值***

双大括号标签会被替换为`msg` 属性的值。同时每次 `msg` 属性更改时它也会同步更新。

```html
<script setup lang="ts">
import {ref} from "vue";
const text = ref<string>("文本插值语法");
</script>

<template>
  {{text}}
</template>
```

***原始 HTML***

1. 有的时候想想渲染源码,有的时候不想渲染源码

2. `v-html`就是指令语法

```html
<script setup lang="ts">
import {ref} from "vue";
const rawHtml = ref<string>("<span style='color: red'>This should be red.</span>");
</script>

<template>
  <p>Using text interpolation: {{ rawHtml }}</p>
  <p>Using v-html directive: <span v-html="rawHtml"></span></p>
</template>
```

***Attribute 绑定***

如果想给html的属性绑定数据要怎么操作呢,下面以给div的id和class属性绑定做演示

```html
<script setup lang="ts">
import {ref} from "vue";
const myId = ref<string>("myId");
const showButton = ref<boolean>(false);
  
type objectOfAttrs={
  id: string,
  class:string
}
  
const objectOfAttrsObj=ref<objectOfAttrs>({
  id: "myId4",
  class:"demo"
});
  
  
</script>

<template>
  <!-- ❎错误做法 -->
  <div id={{myId}}></div>

  <!-- ✅正确做法,如果绑定的值是 null 或者 undefined，那么该 attribute 将会从渲染的元素上移除。 -->
  <div v-bind:id="myId"></div>

  <!-- ✅正确做法,简写写法 -->
  <div :id="myId"></div>

  <!-- ✅正确做法,同名简写写法,就是属性(Id),和等号对应的值是一样的话 -->
  <div :id></div>

  <!-- ✅正确做法,同名简写写法,第二种写法 -->
  <div v-bind:id></div>

  <!-- -------------------------------------------------------- -->

	<!-- 绑定布尔类型 -->
  <button :disabled="showButton">按钮</button>

	<!-- 动态绑定多个值 -->
  <div v-bind="objectOfAttrsObj">动态绑定多个值,只是需要写v-bind</div>
</template>
```

## 响应式基础

这个有点底层,不去了解了,大概意思是简单的数据类型就用ref,对象或者复杂嵌套的结构就用reactive

## 计算属性

***基础示例***

Vue 的计算属性会自动追踪响应式依赖。它会检测到 `authorComputed` 依赖于 `author.books`，所以当 `author.books` 改变时，任何依赖于 `authorComputed` 的绑定都会同时更新。

计算属性会被缓存,并且只调用一次,方法是调用一次执行一次,在例子中,触发了一次页面渲染(页面的数据有变动),方法就会被执行一次

```html
<script setup lang="ts">

import {computed, reactive, ref} from "vue";

type authorType={
  name:string,
  books:string[]
}

const author = reactive<authorType>({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// 计算属性,使用泛型,返回值必须是字符串
const authorComputed = computed<string>(()=>{
  return author.books.length >0?author.books.length.toString():"没有书"
})

function add(){
  author.books.push("Vue 5 - Advanced Guide")
}

// 计算属性,使用泛型,返回值必须是字符串
const myDateTimeComputed = computed<number>(() => {
  return getDateTime();
});

function getDateTime(){
  console.log("方法被调用")
  return Date.now();
}

function myDateTimeMethod(){
  return getDateTime();
}

// 定义一个用于触发重新渲染的状态
const renderCounter = ref(0);
function forceRerender() {
  console.log("12321")
  renderCounter.value++;
}

</script>

<template>
  <!-- 计算属性 -->
  <span>通过计算属性计算出来的结果:{{ authorComputed }}</span>
  <br>
  <button @click="add()">添加数据</button>
  <br>
  <!-- 计算属性和方法的区别 -->
  <button @click="forceRerender">触发重新渲染</button>
  计算属性和方法的区别-计算属性:{{ myDateTimeComputed }}
  计算属性和方法的区别-方法:{{renderCounter}}-{{ myDateTimeMethod() }}
</template>
```

***可写计算属性***

计算属性默认是只读的。当你尝试修改一个计算属性时，你会收到一个运行时警告。只在某些特殊场景中你可能才需要用到“可写”的属性，你可以通过同时提供 getter 和 setter 来创建

```html
<script setup lang="ts">

import {computed, reactive, ref} from "vue";

const firstName = ref('Anthony')

/**
 * 错误的写法
 */
// const fullName = computed(()=>{
//   return firstName.value + ' ' + lastName.value
// })

/**
 * 正确的写法
 */
const fullName = computed({
  // getter
  get() {
    return firstName.value
  },
  // setter
  set(newValue) {
    // 注意：我们这里使用的是解构赋值语法
    firstName.value = newValue
  }
})

/**
 * 这样是不对的
 */
function showWarn(){
  console.log("修改计算属性")
  fullName.value="DiDiDi"
}
</script>

<template>
  演示修改计算属性,控制台报错:{{fullName}}
  <button @click="showWarn">尝试修改计算属性</button>
</template>
```

## Class 与 Style 绑定

太麻烦了,不看了

## 条件渲染

v-if  可以单独使用

v-else-if 要跟v-if 一起使用

v-else 要跟v-if 或者 v-else-if 一起使用

```html
<script setup lang="ts">

import {computed, reactive, ref} from "vue";

const season = ref<number>(6);

</script>

<template>
  <h1 v-if="season >=1 && season <=3">春季</h1>
  <h1 v-else-if="season >=4 && season <=6">不是春季</h1>
  <h1 v-else>数据错误</h1>
</template>
```

## 列表渲染

```html
<script setup lang="ts">

import {computed, reactive, ref} from "vue";

const items = ref([{ message: 'Foo' }, { message: 'Bar' }])

</script>

<template>
  for循环
  <li v-for="(item,index) in items">
    {{index}}-{{ item.message }}
  </li>

  解构函数
  <li v-for="{ message } in items">
    {{ message }}
  </li>

  解构函数,有索引的时候
  <li v-for="({message},index) in items">
    {{index}}-{{ message }}
  </li>
</template>
```

## 事件处理

我们可以使用 `v-on` 指令 (简写为 `@`) 来监听 DOM 事件，并在事件触发时执行对应的 JavaScript。用法：`v-on:click="handler"` 或 `@click="handler"`。

***内联事件处理器***

```html
<script setup lang="ts">

import {computed, reactive, ref} from "vue";
const count = ref(0)
</script>

<template>
  <button @click="count++">Add 1</button>
  <p>Count is: {{ count }}</p>
</template>
```

***方法事件处理器***

```html
<script setup lang="ts">

function greet() {
  console.log("方法事件处理器")
}
</script>

<template>
  <!-- `greet` 是上面定义过的方法名 -->
  <button @click="greet">Greet</button>
</template>
```

## 表单输入绑定
主要是看框架了,先不学了,太多细节了

## 生命周期钩子
这个API好多个,以后慢慢看

## 侦听器

每次修改被监听的对象,就会打印旧值和新值

***基本示例***

```html
<script setup lang="ts">
import {ref, watch} from 'vue'

/*被监听的对象*/
const question = ref<number>(1);

// 可以直接侦听一个 ref
watch(question, async (newQuestion, oldQuestion) => {
  console.log("老值:", oldQuestion)
  console.log("新值:", newQuestion)
})
</script>

<template>
  <input v-model="question"/>
</template>
```

***侦听不同的数据源类型***

`watch` 的第一个参数可以是不同形式的“数据源”：它可以是一个 ref (包括计算属性)、一个响应式对象、一个 getter 函数、或多个数据源组成的数组：

```html
<script setup lang="ts">
import {reactive, ref, watch} from 'vue'

const x = ref<number>(0)
const y = ref<number>(0)

type authorType={
  name: string,
}

const z = reactive<authorType>({
  name:"anthony"
})

// ref
watch(x, (newX,oldValue) => {
  console.log(`监听单个ref,x的旧值是:${oldValue},新值是:${newX}`)
})

// getter 函数
watch(
    () => x.value + y.value,
    (newSum, oldSum) => {
      console.log(`监听getter函数, 原来的和值是:${oldSum},新值是: ${newSum}`)
    }
)

// 多个来源组成的数组
watch([x, () => y.value], ([newX, newY],[oldX, oldY]) => {
  console.log(`监听多个数据源:X的旧值是:${oldX},X的新值是:${newX},Y的旧值是:${oldY},Y的新值是:${newY}`)
})

// 监听reactive
// 错误，因为 watch() 得到的参数是一个 number
// 错误的写法,会提示报错,
// MyClick.vue:35 [Vue warn]: Invalid watch source:  anthony A watch source can only be a getter/effect function, a ref, a reactive object, or an array of these types.
// at <MyClick>
// at <App>
// watch(z.name, (count) => {
//   console.log(`name is: ${count}`)
// })
// 正确的写法
watch(
    () => z.name,
    (nameNew,oldName) => {
      console.log(`name is: ${nameNew},${oldName}`)
    }
)
</script>

<template>
  <input v-model="x" type="number"/>
  <input v-model="y" type="number"/>
  <input v-model="z.name" />
</template>
```

## 模板引用

进入到页面会自动聚焦到输入框中

```html
<script setup>
import { ref, onMounted } from 'vue'

// 声明一个 ref 来存放该元素的引用
// 必须和模板里的 ref 同名
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```

## 组件基础

**定义组件**

```html
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">You clicked me {{ count }} times.</button>
</template>
```

**使用组件**

在父组件中引入

```html
<script setup lang="ts">
import MyClick from "@/components/MyClick.vue";
</script>

<template>
  <MyClick/>
</template>
```

**传递 props**(父传子)

```html
// 子组件
<script setup lang="ts">
import {ref} from 'vue'

const count = ref<number>(0)
defineProps(['title'])
</script>

<template>
  <button @click="count++">{{title}},You clicked me {{ count }} times.</button>
</template>

// 父组件
<script setup lang="ts">
import MyClick from "@/components/MyClick.vue";
</script>

<template>
  <MyClick title="计算器1"/>
  <MyClick title="计算器2"/>
  <MyClick title="计算器3"/>
  <MyClick title="计算器4"/>
</template>
```

**emit**(子传父)

父组件

```html
<script setup lang="ts">

import { ref } from 'vue'
import MyEmit from "@/components/MyEmit.vue";

type Person = {
	name: string,
	age: number
}

const myName = ref<string>('')
const myAge = ref<number>('')

const getSubmit = (data: Person) => {
	myName.value = data.name
	myAge.value = data.age
}

</script>

<template>
	<MyEmit @myEmit="getSubmit" />
	从子组件接收的数据:{{ myName }} {{ myAge }}
</template>
```

子组件

```html
<script setup lang="ts">

type Person = {
	name: string,
	age: number
}

const me = {
	name: 'anthony',
	age: 23
}

const emit = defineEmits<{ (e: 'myEmit', payload: Person): void }>();
emit('myEmit', me)
</script>
```

### $bus 消息总线

main.js

```js
//引入Vue
import Vue from 'vue'
//引入App
import App from './App.vue'
//关闭Vue的生产提示
Vue.config.productionTip = false

//创建vm
new Vue({
	el:'#app',
	render: h => h(App),
	beforeCreate() {
		Vue.prototype.$bus = this //安装全局事件总线
	},
})
```

App.vue

```vue
<template>
	<div class="app">
		<h1>{{msg}}</h1>
		<School/>
		<Student/>
	</div>
</template>

<script>
	import Student from './components/Student'
	import School from './components/School'

	export default {
		name:'App',
		components:{School,Student},
		data() {
			return {
				msg:'你好啊！',
			}
		}
	}
</script>

<style scoped>
	.app{
		background-color: gray;
		padding: 5px;
	}
</style>
```

Student.vue

```html
<template>
	<div class="school">
		<h2>学校名称：{{name}}</h2>
		<h2>学校地址：{{address}}</h2>
	</div>
</template>

<script>
	export default {
		name:'School',
		data() {
			return {
				name:'尚硅谷',
				address:'北京',
			}
		},
		mounted() {
			// console.log('School',this)
			this.$bus.$on('hello',(data)=>{
				console.log('我是School组件，收到了数据',data)
			})
		},
		beforeDestroy() {
			this.$bus.$off('hello')
		},
	}
</script>

<style scoped>
	.school{
		background-color: skyblue;
		padding: 5px;
	}
</style>
```

School.vue

```html
<template>
	<div class="student">
		<h2>学生姓名：{{name}}</h2>
		<h2>学生性别：{{sex}}</h2>
		<button @click="sendStudentName">把学生名给School组件</button>
	</div>
</template>

<script>
	export default {
		name:'Student',
		data() {
			return {
				name:'张三',
				sex:'男',
			}
		},
		methods: {
			sendStudentName(){
				this.$bus.$emit('hello',this.name)
			}
		},
	}
</script>

<style lang="less" scoped>
	.student{
		background-color: pink;
		padding: 5px;
		margin-top: 30px;
	}
</style>
```

### $set

### $nextTick

# 深入组件

## 插槽

**插槽内容与出口**

主文件

```html
<script setup lang="ts">

import { ref, provide, reactive } from 'vue'
import First from "@/components/First.vue";
import MySlotWithName from "@/components/MySlotWithName.vue";
import MySlotWithOutName from "@/components/MySlotWithOutName.vue";

</script>

<template>
	<h1>插槽演示</h1>
  
  <!-- 匿名插槽 -->
	<MySlotWithOutName>
		<a href="https://baidu.com">跳转到baidu</a>
	</MySlotWithOutName>

	<br>

	<MySlotWithName>
    <!-- 具名插槽 -->
		<!-- <template v-slot:url> -->

		<!-- 简写的方式 -->
		<template #url>
			<a href="https://google.com">跳转到谷歌</a>
		</template>
	</MySlotWithName>
</template>
```

MySlotWithOutName.vue

```html
<template>
	匿名插槽
	<slot />
</template>
```

```html
<template>
	具名插槽
	<slot name="url" />
</template>
```

**作用域插槽**

```html
<script setup lang="ts">
import MySlotWithName from "@/components/MySlotWithName.vue";

</script>

<template>
	<MySlotWithName>
		<template #url="data">
			{{ data.title }}- {{ data.age }}
			<a href="https://google.com">跳转到谷歌</a>
		</template>
	</MySlotWithName>
</template>
```

```html
<template>
	具名插槽
	<slot name="url" title="anthony" age="12" />
</template>
```





## 依赖注入

给所有的下级组件,包括直接子组件传递数据和方法

这个例子的最上层组件修改数据之后,可以修改所有组件显示的数据

这个例子的下层组件修改数据之后,也是可以修改所有组件显示的数据

最上层组件

```html
<script setup lang="ts">

import { ref, provide, reactive } from 'vue'
import First from "@/components/First.vue";

type Person = {
	name: string,
	age: number
}

const me: Person = reactive({
	name: 'anthony',
	age: 23
})

provide("provideDemo", me)

const update = () => {
	me.age = me.age + 1
	console.log(me);
}

const updateTwo = () => {
	me.age = me.age + 2
	console.log(me);
}

provide("provideUpdateTwo", updateTwo)

</script>

<template>
	最顶层组件,接收到的数据：{{ me.name }},{{ me.age }}<button @click="update">修改所有层数据+1</button>
	<br>
	<First />
</template>

```

第一层组件

```html
<script setup lang="ts">

import { inject } from 'vue'
import Second from "./Second.vue";

type Person = {
	name: string,
	age: number
}

const me = inject<Person>("provideDemo")
</script>
<template>
	这是第一层,接收到的数据：{{ me.name }},{{ me.age }}
	<br>
	<Second />
</template>
```

第二层组件

```html
<script setup lang="ts">
import { inject } from 'vue'
import Third from "./Third.vue";

type Person = {
	name: string,
	age: number
}

const me = inject<Person>("provideDemo")
</script>
<template>
	这是第二层,接收到的数据：{{ me.name }},{{ me.age }}
	<br>
	<Third />
</template>
```

第三层组件

```html
<script setup lang="ts">
import { inject } from 'vue'

type Person = {
	name: string,
	age: number
}

const me = inject<Person>("provideDemo")
const provideUpdateTwo = inject("provideUpdateTwo")
</script>

<template>
	这是第三层,接收到的数据：{{ me.name }},{{ me.age }}<button @click="provideUpdateTwo">修改最顶层数据+2</button>
</template>
```



# yarn

yarn的安装

```shell
npm install -g yarn
```

yarn常用命令

```shell
// 初始化
yarn init 
// 添加包
yarn add [package]
yarn add [package]@[version]
yarn add [package]@[tag]
// 添加到不同依赖项
yarn add [package] --dev
yarn add [package] --peer
yarn add [package] --optional
// 升级包
yarn upgrade [package]
// 移除依赖包
yarn remove [package]
// 安装所有依赖
yarn 或 yarn install
```

# 报错

1.node新版本引起的报错

```shell
this[kHandle] = new _Hash(algorithm, xofLen);
^

Error: error:0308010C:digital envelope routines::unsupported
```

解决方法1:

```shell
推荐：修改package.json，在相关构建命令之前加入SET NODE_OPTIONS=--openssl-legacy-provider

"scripts": {
   "serve": "SET NODE_OPTIONS=--openssl-legacy-provider && vue-cli-service serve",
   "build": "SET NODE_OPTIONS=--openssl-legacy-provider && vue-cli-service build"
},
```

解决方法2:

降低node版本

# 前端Vue父组件点击按钮打开子组件弹窗案例

父组件

```vue
<template>
    <div>
        # 需要定义ref=child
        <indexChild ref="child"></indexChild>
        <el-button  @click="open">打开弹窗</el-button>
    </div>
</template>
<script>
import indexChild from "../../components/indexChild.vue";
export default {
    components: {
        indexChild
    },
    data () {
        return {
        };
    },
    methods: {
        open () {
            this.$refs.child.open(); // 这样可以直接访问子组件方法,用ref拿子组件方法
        }
    }
}
</script>

```

子组件

```vue
<template>
    <div>
        <el-dialog title="收货地址" :visible.sync="dialogFormVisible">
            <span>这是一段信息</span>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
export default {
    data () {
        return {
            dialogFormVisible: false,
        };
    },
    methods: {
        open () { // 在父组件调用打开
            this.dialogFormVisible = true
        }
    }
};
</script>

```

> 参考:[前端Vue父组件点击按钮打开子组件弹窗案例](https://blog.csdn.net/m0_49714202/article/details/126100398)
