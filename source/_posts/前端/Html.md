---
title: HTML
tags:
  - 前端
  - HTML
categories:
  - HTML
cover: https://image.runtimes.cc/202404051528094.png
abbrlink: 54626
date: 2022-11-19 18:25:00
updated: 2025-05-09 14:57:16
---

```html
<!-- 声明文档类型，表示这是一个 HTML5 文档 -->
<!-- 这个不是标签,这是文档类型的声明标签 -->
<!DOCTYPE html>
<!-- 定义 HTML 文档的根元素，并设置文档的语言为英语 -->
<html lang="en">
<head>
    <!-- 指定文档的字符编码为 UTF-8，支持大多数语言的字符 -->
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
</body>
</html>
```


## 标题标签

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

## 段落标签

```html
<body>
    <p>我们一进里面一片漆黑，然后老师给我们每一个人都发了一个眼镜，我们戴上眼镜，后面前有一张大白纸，后面有一个投影仪，放了一个电影，名叫“雪怪大冒险”。</p>
    <p>有几位老师去帮我们买汉堡去了，我们先到那里先吃一些零食，我们吃饱后，我们一起去玩游戏，第一个游戏的名字叫做动物园里有什么。第二个游戏的名字叫做学校里面有什么</p>
</body>
```

**段落格式的特点**
* 段落内容会自动换行，无需手动添加 `<br>` 标签。
* 段落段落之间会有空隙

## 换行标签

```html
<body>
13213123<br/>3123123
</body>
```

## 文本格式化标签

```html
<body>
	1<strong>加粗</strong>3
	1<b>加粗</b>3
	
	1<em>倾斜</em>3
	1<del>删除线</del>3
	1<ins>下划线</ins>3
</body>
```

## div

```html
<body>
<div>div单独占一行</div>3213123
<div>div单独占一行</div>
</body>
```

## span

所有的span占一行

```html
<body>
<span>div单独占一行</span>3213123
<span>div单独占一行</span>
</body>
```

## 图像标签

```html
<body>
这是百度的图片:<img src="./demo.png"/>
</body>
```
通常只需调整宽度或高度中的一个，另一个会自动计算以保持比例不变。

## 超链接

target属性有_self为默认值,当前页面_blank 新的页面打开

外连接就要加http,内连接就是路径地址

```html
<body>
<a href="http://baidu.com">这是超裂解</a>
</body>
```

## 锚点

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

## 表格标签

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

## 列表标签

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

## 表单标签

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

### input

```html
<body>
	<form>
		用户名:<input type="text" name="myName" value="anthony"><br />
		密码:<input type="password" name="myPassword" value="123456"><br />
		测试重置:<input type="text" name="myTest"><br />
		性别:
		<input type="radio" name="myGender" value="male" checked> 男
		<input type="radio" name="myGender" value="female"> 女
		<br />
		爱好:
		<input type="checkbox" name="myHobby" value="basketball" checked> 篮球
		<input type="checkbox" name="myHobby" value="football"> 足球
		<input type="checkbox" name="myHobby" value="pingpong"> 乒乓球

		<br />
		<input type="submit" value="提交">
		<input type="reset" value="重置">
	</form>
</body>
```
### select
```html
<body>
	<form>
		地区:
		<select name="myArea">
			<option value="beijing">北京</option>
			<option value="shanghai">上海</option>
			<option value="guangzhou" selected>广州</option>
			<option value="shenzhen">深圳</option>
		</select>
	</form>
</body>
```


### 综合练习
```html
<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>综合案例-注册</title>
</head>

<body>
	<h4>青春不常在,赶紧谈恋爱</h4>
	<table border="1" cellspacing="0" cellpadding="0" width="500px">
		<tr>
			<td>性别:</td>
			<td>
				<input type="radio" name="myGender" id="man"><label for="man">男<img src="./assert/male.svg" alt="male"
						width="12px"></label>
				<input type="radio" name="myGender" id="woman"><label for="woman">女<img src="./assert/female.svg"
						alt="female" width="12px"></label>
			</td>
		</tr>

		<tr>
			<td>生日:</td>
			<td>
				<select name="myBirthday">
					<option value="1990" selected>请选择年</option>
					<option value="1991">1991</option>
					<option value="1992">1992</option>
					<option value="1993">1993</option>
					<option value="1994">1994</option>
					<option value="1995">1995</option>
				</select>

				<select name="myBirthday">
					<option value="1" selected>请选择月</option>
					<option value="2">2</option>
					<option value="3">3</option>
					<option value="4">4</option>
					<option value="5">5</option>
					<option value="6">6</option>
				</select>

				<select name="myBirthday">
					<option value="1" selected>请选择日</option>
					<option value="2">2</option>
					<option value="3">3</option>
					<option value="4">4</option>
					<option value="5">5</option>
					<option value="6">6</option>
				</select>
			</td>
		</tr>

		<tr>
			<td>所在地区:</td>
			<td>
				<input type="text" name="myLocation" placeholder="请输入所在地区">
			</td>
		</tr>

		<tr>
			<td>婚姻状态:</td>
			<td>
				<input type="radio" name="myMarriage" id="married"><label for="married">已婚</label>
				<input type="radio" name="myMarriage" id="unmarried"><label for="unmarried">未婚</label>
				<input type="radio" name="myMarriage" id="divorced"><label for="divorced">离异</label>
			</td>
		</tr>

		<tr>
			<td>学历:</td>
			<td>
				<input type="text" name="myEducation" placeholder="请输入学历">
			</td>
		</tr>

		<tr>
			<td>爱好:</td>
			<td>
				<input type="checkbox" name="myHobby"><label for="basketball">篮球</label>
				<input type="checkbox" name="myHobby"><label for="football">足球</label>
				<input type="checkbox" name="myHobby"><label for="pingpong">乒乓球</label>
			</td>
		</tr>

		<tr>
			<td>自我介绍:</td>
			<td>
				<textarea name="myIntro" cols="30" rows="10" placeholder="请输入自我介绍"></textarea>
			</td>
		</tr>

		<tr>
			<td></td>
			<td>
				<input type="submit" name="myCode" value="免费注册">
			</td>
		</tr>

		<tr>
			<td></td>
			<td>
				<input type="checkbox" checked><label for="myAgreement">我同意《用户协议》</label>
			</td>
		</tr>

		<tr>
			<td></td>
			<td>
				<a href="https://www.baidu.com">我是会员,立即登录</a>
			</td>
		</tr>


		<tr>
			<td></td>
			<td>
				<h5>我承诺</h5>
				<ul>
					<li>1. 我是成年人</li>
					<li>2. 我是成年人</li>
					<li>3. 我是成年人</li>
					<li>4. 我是成年人</li>
				</ul>
			</td>
		</tr>

	</table>
</body>

</html>
```

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



