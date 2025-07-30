---
title: ESM
categories:
  - 前端
cover: https://image.runtimes.cc/202404051529338.png
abbrlink: 64129
date: 2025-05-05 15:39:19
updated: 2025-05-05 15:39:20
---
# 导入和导出

Es Module和CommonJs的导入导出不一样

```javascript
// b.js
// 一个模块只能有一个默认输出，export default 命令只能使用一次；
export default function sayHello() {
  console.log('Hello!');
}

export default function sayHello2() {
  console.log('Hello!');
}

// a.js , 运行报错
import sayHello from './b.js';

sayHello();
```

如何导出多个变量呢

```javascript
// b.js
export function sayHello() {
  console.log('Hello!');
}

export function sayHello2() {
  console.log('Hello!');
}

// a.js
import {sayHello} from './b.js';

sayHello();
```

什么时候花括号呢?

```javascript
// b.js
// 对于import命令后面不用加大括号，因为只有唯一对应export default命令。
export default function sayHello() {
  console.log('Hello!');
}

export function sayHello2() {
  console.log('Hello2!');
}

// a.js
// 导入default的变量不用花括号
import sayHello, {sayHello2} from './b.js';

sayHello();
sayHello2();
```

# Promise

基本代码结构

```js
const myPromise = new Promise((resolve, reject) => {
  // 异步操作 (如读取文件、获取数据)
  let success = true;  // 这是模拟的异步操作结果

  if (success) {
    resolve("操作成功！");  // 成功时调用 resolve
  } else {
    reject("操作失败！");   // 失败时调用 reject
  }
});

// 使用 .then() 处理成功的情况，使用 .catch() 处理失败的情况
myPromise.then((result) => {
    console.log(result); // 输出：操作成功！
  })
  .catch((error) => {
    console.log(error);  // 如果失败，则输出错误信息
  });

// 也可以写成这样
myPromise
  .then(
    () => { console.log("操作成功！"); },  // 成功时执行
    () => { console.log("操作失败！"); }   // 失败时执行
  );
```

