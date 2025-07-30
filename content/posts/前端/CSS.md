---
title: CSS
tags:
  - 前端
  - CSS
categories:
  - CSS
cover: https://image.runtimes.cc/202505281524854.webp
abbrlink: 6541
date: 2022-11-19 18:25:00
updated: 2025-05-23 21:47:41
---

# CSS

## 选择器

### 基础选择器

| 选择器名字   | 示例                 |
| ------------ | -------------------- |
| 标签选择器   | `p{color: green;}`   |
| 类选择器     | `.red {color: red;}` |
| ID选择器     | `#red {color: red;}` |
| 通配符选择器 | `* {color: red;}`    |

### 复合选择器

| 选择器     | 作用                   | 描述             |
| ---------- | ---------------------- | ---------------- |
| 后台选择器 | `a b{color: green;}`   | b是孩子          |
| 子代选择器 | `a> b{color: green;}`  | b是儿子          |
| 并集选择器 | `a , b{color: green;}` | 一次选中多个元素 |

### 伪类选择器

- :link 未被访问的连接
- :visited 已经访问的连接
- :hover 鼠标放在上面的连接
- :active 鼠标按下没有谈起的连接

### focus伪类选择器

获取焦点的`表单`选择器
{% iframe https://codepen.io/anthony2023/embed/jOdveJe?default-tab=html%2Cresult&editable=true&theme-id=light%}

## 字体属性
{% iframe https://codepen.io/anthony2023/embed/dyagYoN?default-tab=html%2Cresult&editable=true&theme-id=light%}
## 文本属性
{% iframe https://codepen.io/anthony2023/embed/zYemvvx?default-tab=html%2Cresult&editable=true&theme-id=light%}
## 盒子模型

### 边框
还有个表格边框合并的属性:`border-collapse:`两条边合并成一条边
{% iframe https://codepen.io/anthony2023/embed/eYxPBPx?default-tab=html%2Cresult %}

### 内边距

* 内边距也会影响盒子的实际大小
* padding 值得顺序是 上 右 下 左
* 没有指定width,加了padding就不会改变盒子宽度
{% iframe https://codepen.io/anthony2023/embed/VwgEmRj?default-tab=html%2Cresult%}

### 外边距

margin的语法跟padding用法一样
{% iframe https://codepen.io/anthony2023/embed/PoVyWYO?default-tab=html%2Cresult%}

### 外边距应用-盒子居中
让盒子居中的前提条件是:
- 盒子必须指定了宽度
- 盒子左右的外边距都设置为auto
{% iframe https://codepen.io/anthony2023/embed/BaMqpaY?default-tab=html%2Cresult %}

### 嵌套外边距塌陷
{% iframe https://codepen.io/anthony2023/embed/KKJGadb?default-tab=html%2Cresult&editable=true %}

* 解决方法1: 给父元素定义上边框,坏处就是撑大了盒子
`border-top: 1px solid transparent;`
* 解决方法2:指定一个上内边距,坏处就是撑大了盒子
`padding: 1px;`
* 解决方法三:为父元素添加 overflow
`overflow: hidden;`

在`浮动`,`固定`,`绝对定位`的盒子就不会有塌陷问题

## 元素显示模式

### 块元素

- div ,h1 p ul ol li 一个占一行
- 高度,宽度,外边距,内边距都可以控制
- 宽度默认是容器的100%
- 是一个容器及盒子,里面可以放行内或者块元素
- `<p>`和`<h1>` 等用于存放`文字`的标签里面不能放`<div>`
{% iframe https://codepen.io/anthony2023/embed/oNmaaKb?default-tab=html%2Cresult&editable=true %}

### 行内元素

- span a strong b em i
- 一行可以用多个
- 宽高,设置无效,背景颜色设置有效
- 默认宽度就是它本身内容的宽度
- 行内元素只能放文本或者其他行内元素
{% iframe https://codepen.io/anthony2023/embed/QWYZJEZ?default-tab=html%2Cresult&editable=true %}

### 特殊情况

链接里面不能再放连接,`<a>`里面可以放块级元素

`<input> <img>`是行内款,一行可以给多个,也能设置宽高

### 元素显示模式的转换
{% iframe https://codepen.io/anthony2023/embed/wvNYQoj?default-tab=html%2Cresult&editable=true %}

