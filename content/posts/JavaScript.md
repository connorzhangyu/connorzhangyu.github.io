---
title: JavaScript
id: 04caf71a-7956-47f7-8f51-4546ac1db747
date: 2025-01-04 03:56:03
auther: connor
cover: null
excerpt: title JavaScript tags JavaScript 前端 categories 前端 JavaScript cover https//image.runtimes.cc/202404051529338.png abbrlink 18006 date 2022-11-19 
permalink: /archives/a587d8d7-a6d2-4f81-a79f-8dae40d60a0f
---

---
title: JavaScript
tags:
  - JavaScript
  - 前端
categories:
  - 前端
  - JavaScript
cover: https://image.runtimes.cc/202404051529338.png
abbrlink: 18006
date: 2022-11-19 18:25:00
updated: 2024-10-11 19:26:29
---

# JavaScript
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>setTimeout</title>
    </head>
    <body>
        <input type="text" id="displayBox" name="displayBox" value="0">
        <div id="content"></div>
        <button type="button" onclick="countSecond()">开始计数</button>

        <!--例子4-->
        <button type="button" onclick="clearTimeout(stop)">停止计数</button>

        <script>
            // 例子1
            setTimeout("alert('对不起, 要你久候')", 3000)

            // 例子2
            setTimeout("changeState()", 1000);
            function changeState() {
                let content = document.getElementById('content');
                content.innerHTML = "<div style='color:red'>我是三秒后显示的内容！</div>";
            }

            // 例子3
            x = 0
            function countSecond()
            {
                x = x+1
                document.getElementById("displayBox").value=x
                stop = setTimeout("countSecond()", 1000)
            }


            // 例子5
            setTimeout(demo5, 3000,"1232")
            function demo5(msg) {
                alert(msg)
            }
        </script>
    </body>
</html>
```

## 函数

{% tabs JS函数, 1 %}

<!-- tab JS -->

声明普通函数

```javascript
// 声明函数-没有参数-没有返回值
function showMessage() {
  console.log( 'Hello everyone!' );
}

// 调用函数
showMessage();
```

```javascript
// 声明有参数的函数
function showMessage2(param1,param2){
  console.log(param1, param2);
}

// 调用函数
showMessage2("anthony",23)
```

```javascript
// 声明有默认值的函数
function showMessage2(param1,param2=24){
  console.log(param1, param2);
}

// 调用函数
showMessage2("anthony",23)
showMessage2("anthony")
```

```javascript
// 声明有返回值的函数
function haveReturn(param1,param2){
  return param1 + param2;
}

// 调用函数
console.log(haveReturn(1,2))
```

<!-- endtab -->



<!-- tab JS -->

```ts
/**
 * 计算两个数字的和
 * @param num1 - 第一个加数
 * @param num2 - 第二个加数
 * @returns 返回两个数字的和
 */
function add(num1: number, num2: number): number {
    return num1 + num2;
}

// 调用示例
const result = add(5, 3);
console.log(result);  // 输出: 8
```

<!-- endtab -->

{% endtabs %}

---

函数表达式

```javascript
let let1 = function sayHi(){
    return 10086;
}

// 打印 [Function: sayHi]
// 不像别的语言  调用let1的作用==let1(), js里调用函数就需要()
console.info(let1)

let let2 = let1
console.info(let1());
console.info(let2());
```

回调

```javascript
function ask(question, yes, no) {
    if (question) {
        yes();
    } else {
        no();
    }
}

function showQuestion() {
    return 1 === 1;
}

function showOk() {
    console.info("同意");
}

function showCancel() {
    console.info("不同意");
}

// 用法：函数 showOk 和 showCancel 被作为参数传入到 ask
ask(showQuestion(), showOk, showCancel);
// 也可以写成
ask(1 === 1, function () {
        console.info("同意");
    }, function () {
        console.info("不同意");
    }
)
```

普通函数和函数表达式的区别

```javascript
sayHi("John"); // Hello, John

function sayHi(name) {
  alert( `Hello, ${name}` );
}

----------------------------------------------------
sayHi2("John"); // 报错,函数还没有创建!

let sayHi2 = function(name) {  // (*) no magic any more
  alert( `Hello, ${name}` );
};
```

箭头函数,功能是更简化函数表达式

定义

```javascript
let demo1 = function () {
    console.info("常用的函数表达式")
};


let demo2 =()=>console.info("单行的箭头函数")
let demo3 =()=>{
    console.info("单行的箭头函数第一行")
    console.info("单行的箭头函数第二行")
}

let demo4 = (name, age) => console.info("带参数的箭头表达式", name, age);

demo1();
demo2();
demo3();
demo4("Anthony", 24);
```

重新上面的例子

```javascript
let ask = (question, yes, no) => {
    question ? yes() : no();
}

// 也可以写成
ask(() => 1 === 1,
    () => console.info("同意"),
    () => console.info("不同意")
);
```

## 对象

创建对象

```javascript
let user = {     // 一个对象
    name: "John",  // 键 "name"，值 "John"
    age: 30,        // 键 "age"，值 30
    "likes birds": true  // 多词属性名必须加引号
};

// 访问属性
console.info(user.age)
// 删除属性
delete user.age;
console.info(user.age)
// 访问多词属性
console.info(user["likes birds"])
// 也可以访问普通属性
console.info(user["name"])

// 给对象添加属性,这样不行
user.address="Dubai"
user["sex"]="男"
console.info(user.address)
console.info(user.sex)
```

引用

```javascript
let user = {     // 一个对象
    name: "John",  // 键 "name"，值 "John"
    age: 30,        // 键 "age"，值 30
};

let user1 = user
console.info(user1.name)

user.name="anthony"
console.info(user1.name)

// 比较两个对象
console.info(user === user1)

// 浅拷贝,克隆对象,克隆user 给 user2
let user2={}
Object.assign(user2, user);
console.info(user2.name)
user.name="Nick"
console.info(user2.name)
```

方法

```javascript
let user = {     // 一个对象
    name: "John",  // 键 "name"，值 "John"
    age: 30,        // 键 "age"，值 30
    // 给对象添加方法的第三种写法
    sayHi3: function () {
        console.info(this.name)
    },
    sayHi4: function () {
        let demo = ()=>{
            console.info("箭头函数不能访问this");
        }
        demo();
    },
};
// 给对象添加方法的第一种写法
user.sayHi = function () {
    console.info(this.name)
};

// 给对象添加方法的第二种写法
function sayHi2() {
    console.info(this.name)
}

user.sayHi2 = sayHi2;

// 调用对象方法
user.sayHi()
user.sayHi2()
user.sayHi3()
user.sayHi4()
```

数组

```javascript
// 数组
let arr = [];
// 添加新元素
arr[0] = "A"
arr[1] = "B"
arr[2] = "C"
console.info("获取指定元素",arr[0])
console.info("打印元素个数",arr.length)
console.info("打印全部元素",arr)

// 数组作为队列(push/pop),也可以做为栈(shift和unshift)
arr.push("哈哈哈,存入输入")
console.info("打印全部元素",arr)
// 取出数据
arr.pop()
console.info("打印全部元素",arr)
```

map

```javascript
// map
let map = new Map();
map.set("k1","v1")
map.set("k2","v2")
map.set("k3","v3")

// 访问数据
console.info(map)
console.info(map.get("k1"))
console.info(map["k1"]) // 这样不行

// 遍历
for (const key of map.keys()) {
    console.info(map.get(key))
}
```


