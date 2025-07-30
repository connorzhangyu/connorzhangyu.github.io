---
title: Html
id: c146350e-3454-46ee-88e3-a1351608750b
date: 2025-01-04 03:56:03
auther: anthony
cover: null
excerpt: title HTML tags JavaScript 前端 CSS categories 前端 HTML cover https//image.runtimes.cc/202404051528094.png abbrlink 54626 date 2022-11-19 182500
---

---
title: HTML
tags:
  - JavaScript
  - 前端
  - CSS
categories:
  - 前端
  - HTML
cover: https://image.runtimes.cc/202404051528094.png
abbrlink: 54626
date: 2022-11-19 18:25:00
updated: 2023-12-01 18:46:29
---

# HTML

### 标题标签

```html
<body>
    <h1>111111111111</h1>
    <h2>111111111111</h2>
    <h3>111111111111</h3>
    <h4>111111111111</h4>
    <h5>111111111111</h5>
    <h6>111111111111</h6>
</body>
```

### 段落标签

```html
<body>
<p>我们一进里面一片漆黑，然后老师给我们每一个人都发了一个眼镜，我们戴上眼镜，后面前有一张大白纸，后面有一个投影仪，放了一个电影，名叫“雪怪大冒险”。我们看着入迷，还有电影里面在下雪，我戴着眼镜，却以为天上正在下雪，我伸出手来去捉雪花，我说怎么也捉不住，我一想这是电影里面的，这不是现实。下一站去野餐。</p>

<p>有几位老师去帮我们买汉堡去了，我们先到那里先吃一些零食，我们作文https://Www.ZuoWEn8.Com/的午餐特别丰富，我们吃饱后，我们一起去玩游戏，第一个游戏的名字叫做动物园里有什么。第二个游戏的名字叫做学校里面有什么。第三个游戏的名字叫做萝卜蹲。最后一个游戏是猜谜语，我们玩的特别开心，我们玩猜谜语时，每答对一个问题，都有一个奖品。高年级是苏家和笔袋儿，低年级是彩铅和笔记本儿，我们玩儿了一会儿，我们又开始吃水果了，我们吃西瓜，那瓜可甜了。吃完后我们收拾了一下东西，去了下一站，鸳鸯湖去参观。</p>
</body>
```

### 换行标签

```html
<body>
13213123<br/>3123123
</body>
```

### 文本格式化标签

```html
<body>
	1<strong>加粗</strong>3
	1<b>加粗</b>3

	1<em>倾斜</em>3
	1<del>删除线</del>3
	1<ins>下划线</ins>3
</body>
```

### div

```html
<body>
<div>div单独占一行</div>3213123
<div>div单独占一行</div>
</body>
```

### span

span所有的占一行

```html
<body>
<span>div单独占一行</span>3213123
<span>div单独占一行</span>
</body>
```

### 图像标签

```html
<body>
这是百度的图片<img src="http://www.baidu.com/img/pc_2e4ef5c71eaa9e3a3ed7fa3a388ec733.png"/>
</body>
```

### 超链接

target属性有_self为默认值,当前页面_blank 新的页面打开

外连接就要加http,内连接就是路径地址

```html
<body>
<a href="http://baidu.com">这是超裂解</a>
</body>
```

### 锚点

```html
<body>
目录:
<a href="#1">1</a>
<a href="#2">2</a>
<hr>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<h2 id="1">这是锚点1</h2>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<h2 id="2">这是锚点2</h2>
</body>
```

### 表格标签

```html
<body>
<table border="1" align="center">
    <tr>
        <th>姓名</th>
        <th>性别</th>
    </tr>
    <tr>
        <td>anthony</td>
        <td>南</td>
    </tr>
</table>
</body>
```

### 列表标签

```html
<body>
<!-- 无序列表 -->
<ul>
    <li>无序列表1</li>
    <li>无序列表2</li>
    <li>无序列表3</li>
    <li>无序列表4</li>
</ul>

<!-- 有序列表 -->
<ol>
    <li>1</li>
    <li>2</li>
    <li>4</li>
    <li>3</li>
</ol>

<!-- 自定义 -->
<dl>
    <dt>这是自定义列表</dt>
    <dd>1</dd>
    <dd>2</dd>
    <dd>4</dd>
    <dd>3</dd>
</dl>
</body>
```

### 表单标签

```html
<form action="url地址" method="提交方式" name="表单名称">
    <!-- 点击lable 就能把光标古交到输入框里面-->
    <label for="usernameId">用户名</label>
    <input type="text" name="username" value="anthony" id="usernameId"> <br>
    密码  :<input type="password" name="pwd"> <br>
    性别 : 男<input type="radio" name="sex" value="男" checked>  女<input type="radio" value="女" name="sex"><br>
    爱好: 吃饭<input type="checkbox" name="hobby" value="1">
    睡觉<input type="checkbox" name="hobby" value="2">
    玩<input type="checkbox" name="hobby" value="3"><br>

    <label for="selectId">选项:</label>
    <select id="selectId">
        <option value="1" >一</option>
        <option value="2" selected>二</option>
        <option value="3">三</option>
    </select><br>

    文本域元素:<textarea>23123</textarea>
</form>
```

# JavaScript

动态修改html标题

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>欢迎来到我的网页</title>
</head>
<body>
<h1>试着切换标签页吧！</h1>

<script>
  // 记录原始标题
  var originalTitle = document.title;

  // 定义当页面可见性发生变化时的事件处理函数
  document.addEventListener('visibilitychange', function() {
    if (document.hidden) {
      // 如果页面不可见，修改标题
      document.title = "我等着你回来";
    } else {
      // 如果页面可见，恢复原始标题
      document.title = originalTitle;
    }
  });
</script>
</body>
</html>
```


