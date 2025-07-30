---
title: Python
tags:
  - Python
  - 教程
  - 笔记
categories:
  - Python
cover: https://image.runtimes.cc/202404051532070.png
abbrlink: 43687
date: 2022-11-19 18:25:00
updated: 2023-11-07 11:37:43
---

# 安装

# 虚拟环境

```
# 判断有没有虚拟环境
virtualenv -V

# 安装虚拟环境
# 需要sudo
pip install virtualenv
pip install virtualenvwrapper

# 查看有多少虚拟环境的文件夹
workon

# 创建虚拟环境文件夹
mkvirtualenv 文件夹名字

# 从虚拟文件夹退出
deactiave

# 进入虚拟环境中
workon 虚拟环境名称

# 删除虚拟环境
rmvirutalenv

# 查看虚拟环境有哪些框架,要在虚拟环境中执行
pip freeze

# 安装软件指定版本,要在虚拟环境中执行
pip install flask==10.0.0.0

# 导出虚拟环境中的所有扩展,要在虚拟环境中执行
pip freeze > requirements.txt

# 安装,要在虚拟环境中执行
pip install -r requirements.txt
```

# centos7安装python3

特别是在喜欢环境中已经安装的python2.x的版本中

```
# 这个可能不一定要装
sudo yum -y groupinstall "Development tools"

# 需要的
sudo yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel

# 下载安装包
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0a1.tar.xz

# 解压
tar -xvxf  Python-3.7.0a1.tar.xz

# 复制文件夹
mv Python-3.7.0 /usr/local

# 进入到文件夹
cd /usr/local/Python-3.7.0/

# 编译,一定需要后面的参数
./configure --prefix=/usr/local/bin/python3
make & make install

# 添加软连接
ln -s /usr/local/bin/python3/bin/python3 /usr/bin/python3
ln -s /usr/local/bin/python3/bin/pip3 /usr/bin/pip3

# 验证
python3
pip3
```

# 基础

# 1.注释

```python
# 注释后面需要一个空格
print("单行注释")

print("单行注释")  # 单行注释和代码之间至少要有两个空格

"""
多行注释
"""
print("这是多行注释")
```

# 2.算数运算符

乘法的使用,用`*`可以拼接字符串

```
In [1]: "A" * 30
Out[1]: 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAA'

In [2]: 1 * 30
Out[2]: 30
```

优先级

`幂` >`*` > `/`  > `%` > `//` >`+` > `-`

# 3.变量

### 3.1.变量的类型

数字型

- 整数 int
- 浮点 float(`计算要小心`)
- 布尔
    - 布尔值可以用 `and`、`or` 和 `not` 运算
- 复数(用于科学技术的)

非数字型

- String（字符串）
- List（列表）
- Tuple（元组）
- Dictionary（字典）

### 3.2.type函数

```python
# 整数
print(type(1))
# 浮点数
print(type(1.5))
# 字符串
print(type("hello world"))
# 空值
print(type(None))
```

### 3.3.不同类型的变量之间的计算

```
In [10]: age =13

In [11]: sex =True

In [12]: height = 180.1

In [13]: age + sex
Out[13]: 14

In [14]: age + height
Out[14]: 193.1

In [15]: age + height
Out[15]: 193.1

# 字符串的拼接
In [17]: first_name ="东尼"
In [18]: last_name="安"
In [19]: last_name+first_name
Out[19]: '安东尼'

# 字符串不能和数字相加
In [20]: last_name + 10
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-20-45feb354f2d0> in <module>
----> 1 last_name + 10

TypeError: can only concatenate str (not "int") to str
```

### 3.4.变量的输入

```
# 可以不需要参数
In [24]: input()
123

# 加参数,做提示
In [27]: pwd = input("输入数字")
输入数字123

In [28]: pwd + "456"
Out[28]: '123456'
```

### 3.5.数据类型转换

| 方法名 | 含义 |
| --- | --- |
| str() | 转成字符串 |
| float() | 转成浮点 |
| int() | 转成int |

```
# 数字和字符串相互转
In [3]: age = 23

In [4]: type(age)
Out[4]: int

In [5]: age_str = str(age)

In [6]: type(age_str)
Out[6]: str

In [7]: type(int(age_str))
Out[7]: int

# 浮点转数值,缺失精度
In [9]: pi = "3.14"
In [10]: pi
Out[10]: '3.14'

In [13]: type(float(pi))
Out[13]: float

In [15]: int(float(pi))
Out[15]: 3
```

### 3.6.变量的格式化输出

| 符号 | 描述 |
| --- | --- |
| %c | 格式化字符(输出数值对应的ASCII码) |
| %s | 格式化字符串 |
| %d | 格式化整数(%06d,不足的补0) |
| %x | 格式化十六进制数（小写） |
| %X | 格式化十六进制数（大写） |
| %o | 格式化八进制数 |
| %f | 格式化浮点数字，可以指定小数点精度(%.2f) |
| %% | 输出%号 |

```python
"""'
我的名字是:anthony,请多多关照
我的学号是:000001,请多多关照
单价是:1.00,购买了2.00斤,总价是:2.000
数据的比例是:20%
"""
print("我的名字是:%s,请多多关照" % "anthony")
print("我的学号是:%06d,请多多关照" % 1)
print("单价是:%.2f,购买了%.2f斤,总价是:%.3f" % (1,2,2))
print("数据的比例是:%02d%%" % 20)
```

### 3.7.变量的命名

1. 字母,和下划线和数字组成
2. 不能以数字开始
3. 不能与关键字重名
4. 其余的符号都不行
5. 区分大小写
6. `=`左右都要添加空格
7. 两个单词之间用`_`

# 4.条件控制

### 4.1.if

```python
age = 15
if age >= 18:
    print("成年了")

    print("在一个缩进的,是一个代码块")
elif age<=18:
    print("没有成年")
else:
    print("输入错误")
print("结束了")
```

### 4.2.逻辑运算符

```python
print(True and False)
print(True or False)
print(not True)
```

### 4.3.随机数

```python
In [17]: import random

In [18]: random.randint(12,20)
Out[18]: 12
```

### 4.3.while

```python
i = 0
while i < 5:
    print("....")
    i += 1
```

### 4.4.continue 和 break

如果是嵌套循环,用这个两个关键字,也只是结束当前的循环,不会影响外层的循环

# 5.函数

### 5.1.函数注释

```python
# 这个也是可以注释的
def test():
    """这个也是可以注释的"""
    print("打印乘法表")
```

### 5.2.函数调用

index.py

```
def chengfabiao():
    print("打印乘法表")
```

test.py

```
import index
index.chengfabiao()
```

### 5.3.局部方法修改全局变量

```
num =10

def mo():
    # 声明num是全部变量
    global num
    num=100
    print(num)

mo()
print(num)
```

### 5.4.多个返回值

```python
def change(x,y):
    return y,x

x =1
y=2
x,y = change(1,2)
print(x)
print(y)
```

### 5.5.缺省函数

```
def measure(age,gender=1):
    print(age)
    print(gender)

def measure2(age,gender=1,name="anthony"):
    print(age)
    print(gender)
    print(name)

measure(1)
measure(1,2)

# 有多个缺省的时候,需要指定参数名称
measure2(1,name="anthonyyang",gender=2)

# 拆包
measure(1,*(2,3,4),**{"name":"anthony"})
```

### 5.6.多值参数

习惯存元祖的使用用`*args`,存字典的时候用`**args`

### 5.7.函数的参数-默认参数

```python
def my_print(name,age):
    print(name,age)

def my_print2(name,age=13):
    print(name,age)

# 有默认值的形参,不能放在前面,会报错
# def my_print3(age=13,name):
#    print(name,age)

my_print("anthony",12)

my_print2("anthony2",123)
my_print2("anthony2")
```

### 5.8.函数的参数-关键字参数

```python
def my_print(name,address):
    print(name,address)

my_print("anthony","广东")
my_print("广东","anthony")

my_print(name="anthony",address="广东")
my_print(address="广东",name="anthony",)

# ----------------**kw是关键字参数，且 hobby 就是一个 dict （字典）-------------

def my_print2(name,address,**kw):
    if 'age' in kw:
        print("age=",kw["age"])
my_print2("anthony","广东",age=123)
my_print2("anthony","广东",kw={"age":123})
```

### 5.8.函数的参数-**只接受关键字参数**

```python
def my_print(name,*,address):
    print(name,address)

# 报错
# my_print("anthony","广东")
# my_print("广东","anthony")

my_print("anthony",address="广东")
my_print(address="广东",name="anthony")
```

### 5.8.函数的参数-**不定长参数**

`hobby`是可变参数，且 hobby 其实就是一个 tuple （元祖)

```python
def print_user_info( name ,  age  , sex = '男' , * hobby):
    # 打印用户信息
    print('昵称：{}'.format(name) , end = ' ')
    print('年龄：{}'.format(age) , end = ' ')
    print('性别：{}'.format(sex) ,end = ' ' )
    print('爱好：{}'.format(hobby))
    return;

# 调用 print_user_info 函数
print_user_info( '两点水' ,18 , '女', '打篮球','打羽毛球','跑步
```

# 6.容器

### 6.1.列表

虽然列表可以存储不同类型的数据,但是在开发中,存储的都是相同类型数据,因为要迭代

```python
mylist=["a","b","c"]
print(mylist)

# 通过索引,访问列表中的值
print(mylist[1])

# 通过方括号的形式来截取列表中的数据,访问列表中的值
# 就是从第 0 个开始取，取到第 2 个，但是不包含第 2 个
print(mylist[0:2])

# 通过索引对列表的数据项进行修改或更新
mylist[1] = "bb"
print(mylist)

# 使用 append() 方法来添加列表项
mylist.append("d")
print(mylist)

# 使用 del 语句来删除列表的的元素
del mylist[3]
print(mylist)

# 列表长度
print(len(mylist))
```

### 6.2.元祖

元祖用的是括号

与列表比较,元祖元素不能修改

```python
# 创建元祖方法1
tuple1=('两点水','twowter','liangdianshui',123,456)
tuple2='两点水','twowter','liangdianshui',123,456

# 创建元祖方法2
tuple3 = ()

# 创建元祖方法3
tuple4 = (123,)

print(tuple1)
print(tuple2)
print(tuple3)
print(tuple4)

# 访问元祖
print(tuple1[1])

# 修改元祖的值
mylist=[1,2,3]
tuple5=("ddd",mylist)
print(tuple5)
mylist[1]=43
print(tuple5)

# 删除元祖,tuple 元组中的元素值是不允许删除的，但我们可以使用 del 语句来删除整个元组
del tuple1
```

元祖和列表相互转换

```python
# 声明元祖
In [54]: num_list = (1,2,3,4)

In [55]: type(num_list)
Out[55]: tuple

# 元祖转成列表
In [56]: my_list = list(num_list)

# 修改值
In [57]: my_list[0]=5

# 再转成元祖
In [58]: print(tuple(my_list))
(5, 2, 3, 4)
```

### 6.2.字典

列表是有序的

字典是无序的

```python
names={"name":"xiaoming","age":"23"}

# 取值
print(names["name"])

# 新增和修改(key存在,就是新增,不存在就是修改)
names["address"] ="feilvb"
names["name"] ="anthony123"
print(names)

# 删除
names.pop("name")
print(names)

# 统计键值对的数量
print(len(names))

# 合并键值对,如果合并的时候有相同的key,那个value就是更新值
temp = {"a":"b"}
names.update(temp)
print(names)

# 遍历字典
for k in names:
    print("遍历",k,names[k])

# 清空字典
names.clear()
```

### 6.3.set

set可以理解为只有key的字典

```python
# 创建set
set1 = set([1,2,3])

# 添加元素
set1.add(200)

# 删除元素
set1.remove(1)
```

### 6.4.字符串

```python
str ="hello hello"
print("字符串长度",len(str))
print("字符串出现次数",str.count("llo"))
print("取索引",str.index("llo"))
print("取值",str[1])
# 换行符,都是空白字符
print("判断空白字符",str.isspace())
print("是否以指定字符串开始",str.startswith("hello"))
print("是否以指定字符串结束",str.endswith("LLO"))
print("查找指定字符串",str.find("llo"))
print("替换字符串",str.replace("hello","HELLO"))
print(str[0:9:2])

# bytes转字符串
print(b"abcde".decode("utf-8"))
```

字符串前加 b:b 前缀代表的就是bytes

字符串前加 r:r/R:非转义的原始字符串

# 7.公共方法

1. 内置函数:
- len
- max 只能比较字典的key
- min 只能比较字典的key

2.字符串,列表,元祖都可以切片

3.查看地址值

```
id(str)
```

# 面向对象

类名需要大驼峰命名法

# 1.基本语法

### 1.1.创建对象

```python
class Cat:

    def eat(self):
        print("小猫爱吃鱼")

    def drink(self):
        print("小猫爱喝水")

tom = Cat()
tom.eat()
tom.drink()
```

### 1.2.对象内置方法(魔术方法)

```python
class Cat:

		# 构造方法
    def __init__(self,name):
        print("初始化方法")
        self.name=name

		# 成员方法
    def eat(self):
        print(self.name+"爱吃鱼")

		# 成员方法
    def drink(self):
        print(self.name+"爱喝水")

		# 魔术方法
    def __del__(self):
        print("销毁方法")

		# 魔术方法
    def __str__(self):
        return "重写tostring"

tom = Cat("Tom")
tom.eat()
tom.drink()
print(tom)
```

### 1.3.私有属性和方法

```python
class Cat:

    # 构造方法
    def __init__(self,name):
        print("初始化方法")
        self.name=name
        self.__age =18

    def eat(self):
        print(self.name+"爱吃鱼")

    def drink(self):
        print(self.name+"爱喝水")

    def say_age(self):
        print("年纪是:"+str(self.__age))
        # 调用私有方法
        self.__private_method()

    def __private_method(self):
        print("私有方法")

    def __del__(self):
        print("销毁方法")

    def __str__(self):
        return "重写tostring"

tom = Cat("Tom")
tom.eat()
tom.drink()
tom.say_age()
print(tom)
# 这种访问方式,也是可以访问到私有的属性和方法的
print(tom._Cat__age)
```

### 1.4.继承和重写

```python
class Animal:

    def __init__(self):
        self.name1 =100
        self.__num2 = 200

    def eat(self):
        print("动物吃")

    def run(self):
        print("动物跑")

    # 子类不允许调用私有方法
    def __test(self):
        print("父类可以访问到私有属性和私有方法")

class Dog(Animal):

    def run(self):
        print("子类打印,开始调用父类方法")
        super().run()
        print("调用完父类方法")

# animal = Animal()
# animal.eat()
# animal.run()

dog = Dog()
dog.eat()
dog.run()
```

### 1.5.多继承

尽量避免使用多继承,如果继承了两个累,两个类有相同的方法和属性,容易混淆

```python
class Animal:

    def __init__(self):
        self.name1 = 100
        self.__num2 = 200

    def eat(self):
        print("动物吃")

    def run(self):
        print("动物跑")

    # 子类不允许调用私有方法
    def __test(self):
        print("父类可以访问到私有属性和私有方法")

class Zoo:

    def eat(self):
        print("动物园吃饭")

class Dog(Animal, Zoo):

    def run(self):
        print("子类打印,开始调用父类方法")
        super().run()
        print("调用完父类方法")

dog = Dog()
dog.eat()
```

### 1.6.多态

```python
class Dog(object):

    def __init__(self,name):
        self.name = name

    def game(self):
        print("蹦蹦跳跳",self.name)

class Xiaotianquan(Dog):

    def game(self):
        print("哮天犬",self.name)

class Person(object):

    def __init__(self,name):
        self.name = name

    def game_with_dog(self,dog):
        print("人和狗玩耍",self.name,dog.name)

        dog.game()

# dog = Dog("旺财")
dog = Xiaotianquan("旺财")

xiaoming = Person("xiaoming")
xiaoming.game_with_dog(dog)
```

### 1.7.类属性和类方法和静态方法

类属性 相当于静态变量

```python
class Dog(object):
    # 类属性
    age = 12

    def __init__(self,name):
        self.name = name
```

类方法

```python
class Dog(object):

    # 类属性
    age = 12

    # 类方法
    @classmethod
    def show_age(cls):
        print("静态方法",cls.age)

dog = Dog()
Dog.show_age()
```

静态方法,在不用方法类属性和静态属性的时候,可以定义成静态方法

```python
class Dog(object):

    # 类属性
    age = 12

    # 类方法
    @classmethod
    def show_age(cls):
        print("类方法",cls.age)

    @staticmethod
    def static_method():
        print("静态方法")

dog = Dog()

# 调用类方法
Dog.show_age()
# 调用静态方法
Dog.static_method()
```

# 2.异常

### 2.1.异常的完整语法

```python
try:
    num = int(input("输入一个整数:"))
    10 / num
except ZeroDivisionError:
    print("请不要输入数字0")
except Exception as result:
    print("未知错误 %s" % result)
else:
    print("没有异常才会执行的代码")
finally:
    print("无论是否有异常,都会异常的代码")
```

### 2.2.主动抛异常

```python
def check(name):
    if(name == "anthony"):
        return "是安东尼"
    else:
        # 主动抛异常
        raise Exception("不是安东尼")

# 捕获异常
try:
    print(check("anthony2"))
except Exception as result:
    print(result)
```

# 3.模块

导入的语法如下：
[`from` 模块名】`import` [模块 1类1变量1函数1x[as别名]
常用的组合形式如：

- import 模块名
- from 模块名 import 类、变量、方法等
- from 模块名 import *
- import 模块名 as 别名
- from 模块名import 功能名 as 别名

### 3.1.导入模块

不推荐使用`,`

```
import pkg1
import pkg2
```

### 3.2.简单的使用

my_module.py

```
title = "模块2"

def say_hello():
    print("i am module : %s " % title)

class Cat:
    pass
```

index.py

```
import my_module

# use module method
my_module.say_hello()

dog = my_module.Cat()
print(dog)
```

### 3.3.导入的时候也可以起别名

别名要使用大驼峰命名

```
import my_module as MyModule
```

### 3.4.from...import

导入一部分工具

使用的时候,就不需要写那个模块名了,直接使用

```
from my_module import say_hello
from my_module import Cat
say_hello()
cat = Cat()
```

### 3.5.作为模块的正常写法

```python
def main():
    pass

# 有了这个之后,被别的模块调用的时候
if __name__ = "__main__"
 main
```

### 3.6.包

包 包含多个模块

创建一个新的文件夹,在文件夹里面创建`__init__.py`

```
# . 是相对路径名
from . import send_message
from . import receive_message
```

在文件夹里面创建两个模块

receive_message.py

```
def receive():
    print("接受信息")
```

send_message.py

```
def send(text):
    print("发送 %s" % text)
```

调用模块

```
import hm_message

hm_message.send_message.send('hello')
hm_message.receive_message.receive()
```

### 3.7.发布模块

1.创建setup.py

```
from distutils.core import setup

setup(name="hm_message",
      version="1.0",
      description="push",
      py_modules=["hm_message.send_message",
                  "hm_message.receive_message"])
```

2.命令

```
python setup.py build
python setup.py sdist
```

### 3.8.安装模块

```bash
# 手动安装模块
tar xxx.tar.gz
python setup.py install
```

# pymssql

访问`https://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql`

下载`[pymssql‑2.1.4‑cp38‑cp38‑win32.whl]`

# 网络编程

# 0.****socket的历史****

套接字起源于 20 世纪 70 年代加利福尼亚大学伯克利分校版本的 Unix,即人们所说的 BSD Unix。 因此,有时人们也把套接字称为“伯克利套接字”或“BSD 套接字”。

一开始,套接字被设计用在同 一台主机上多个应用程序之间的通讯。这也被称进程间通讯或IPC。

套接字有两种（或者称为有两个种族）,分别是基于文件型的和基于网络型的。

基于文件类型的套接字家族 - 套接字家族的名字：AF_UNIX unix一切皆文件，基于文件的套接字调用的就是底层的文件系统来取数据，两个套接字进程运行在同一机器，可以通过访问同一个文件系统间接完成通信

基于网络类型的套接字家族 - 套接字家族的名字：AF_INET (还有AF_INET6被用于ipv6，

还有一些其他的地址家族，不过，他们要么是只用于某个平台，要么就是已经被废弃，或者是很少被使用，或者是根本没有实现，所有地址家族中，AF_INET是使用最广泛的一个，python支持很多种地址家族，但是由于我们只关心网络编程，所以大部分时候我么只使用AF_INET)

套接字把复杂的TCP/IP协议族隐藏在Socket接口后面，对用户来说，一组简单的接口就是全部，让Socket去组织数据，以符合指定的协议。

所以，我们无需深入理解tcp/udp协议，socket已经为我们封装好了，我们只需要遵循socket的规定去编程，写出的程序自然就是遵循tcp/udp标准的

# 1.udp

发送端

```
import socket

def main():
    # 创建一个udp套接字
    udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

    while True:

        send_data = str = input("输入要发送的数据")

        if send_data == "exit":
            break

        # udp_socket.sendto(b"这是消息",("192.169.0.1",8000))
        udp_socket.sendto(send_data.encode("utf-8"),("127.0.0.1",7788))

    # 关闭套接字
    udp_socket.close()

if __name__ == '__main__':
    main()
```

接受者

```
import socket

def main():
    # 创建一个udp套接字
    udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

    # 绑定本地相关信息
    local_addr = ("",7788)
    udp_socket.bind(local_addr)

    while True:
        # 等待接收对方发送的数据
        recv_data = udp_socket.recvfrom(1024)
        print(recv_data[0].decode("gbk"))

    # 关闭套接字
    udp_socket.close()

if __name__ == '__main__':
    main()
```

# 2.tcp

# 3.socket

使用socket访问redis

```python
import socket

host = '10.0.2.110'
port = 6379
buf_size = 1
conn = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
conn.connect((host, port))

cmd = 'SELECT 2\n'.encode('utf-8')
r = conn.sendall(cmd)

cmd = 'PING\n'.encode('utf-8')
conn.sendall(cmd)
while True:
    res = conn.recv(buf_size)
    print(res)
    if not res:
        break
conn.close()
```

**服务端套接字函数**
```python
s.bind() #绑定(主机,端口号)到套接字

s.listen() #开始TCP监听

s.accept() #被动接受TCP客户的连接,(阻塞式)等待连接的到来

**客户端套接字函数**

s.connect() #主动初始化TCP服务器连接

s.connect_ex() #connect()函数的扩展版本,出错时返回出错码,而不是抛出异常

**公共用途的套接字函数(客户端和服务端都能使用)**

s.recv() #接收TCP数据

s.send() #发送TCP数据(send在待发送数据量大于己端缓存区剩余空间时,数据丢失,不会发完)

s.sendall() #发送完整的TCP数据(本质就是循环调用send,sendall在待发送数据量大于己端缓存区剩余空间时,数据不丢失,循环调用send直到发完)

s.recvfrom() #接收UDP数据

s.sendto() #发送UDP数据

s.getpeername() #连接到当前套接字的远端的地址

s.getsockname() #当前套接字的地址

s.getsockopt() #返回指定套接字的参数

s.setsockopt() #设置指定套接字的参数

s.close() #关闭套接字
```


# Request

# post请求参数

```python
# 表单提交
import requests

data = {
    "name":"anthony",
    "age":"12"
}

requests.post(url=url,data=data)

# json提交
import requests

json_data = {
    "name":"anthony",
    "age":"12"
}
requests.post(url=url,json=json_data)
```

# cookie操作

```python
cookies={
    'auther':'11223'
}
request.post(url=url,data=data,cookies=cookies)
```

# headers

```python
headers ={
    'auth':'123'
}
request.post(url=url,data=data,cookies=cookies,headers=headers)
```

# 请求超时

```python
# 如果超过2s就超时报错
requests.post(url=url,timeout=2)
```

# 鉴权

有些页面,需要,比如spring secuity的页面

```python
requests.post(url=url,timeout=2,auth=('anthony','123456'))
```

# 编码

```python
# 先编码 再解码
r.text.encode('utf-8').decode(unicode_escape)
```

# 下载流2

```python
url = "http://wx4.sinaimg.cn/large/d030806aly1fq1vn8j0ajj21ho28bduy.jpg"

rsp = requests.get(url, stream=True)
with open('1.jpg', 'wb') as f:
		# 边下载边存硬盘, chunk_size 可以自由调整为可以更好地适合您的用例的数字
    for i in rsp.iter_content(chunk_size=1024):  
        f.write(i)
```

`requests.get(url)`默认是下载在**内存**中的，下载完成才存到硬盘上

`Response.iter_content`来边下载边存硬盘

# Flask

# 路由

### 入门

```
from flask import Flask

app = Flask(__name__)

@app.route('/login')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

### URL上动态参数

```python
from flask import Flask

app =  Flask(__name__)

@app.route("/")
def hello_world():
    return "hello world"

@app.route("/<int:age>")
def play_game(age):
    return "hello world"+str(age)

if __name__ == '__main__':
    app.run(debug=True)
```

### 自定义转换器

```
from flask import Flask
from werkzeug.routing import BaseConverter

app = Flask(__name__)

# 自定义,并且继承BaseConverter
class MyConverter(BaseConverter):

    def __init__(self, map, regex):
        # map 就是app.url_map  也就是请求路径
        # regex,就是d{3}, 也就是自定义的规则
        super(MyConverter, self).__init__(map)
        self.regex = regex

app.url_map.converters["haha"] = MyConverter

@app.route('/<haha("\d{3}"):age>')
def play_game2(age):
    return "自定义转换器,接收3位" + str(age)

@app.route('/<haha("\d{4}"):age>')
def play_game3(age):
    return "自定义转换器,接收4位" + str(age)

if __name__ == '__main__':
    app.run(debug=True)
```

### 给路由添加请求方式

```
from flask import Flask

app = Flask(__name__)

@app.route('/',methods=['POST'])
def play_game2(age):
    return "自定义转换器,接收3位" + str(age)

if __name__ == '__main__':
    app.run(debug=True)
```

### 返回响应体

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def method1():
    """直接返回字符串信息"""
    return "返回字符串信息"

@app.route('/2')
def method2():
    """直接返回字符串信息"""
    return "返回字符串信息",666

@app.route('/3')
def method3():
    """直接返回字符串信息"""
    return {"name":"anthony"},200,{"Content-Type":"application/json","token":"123456"}

if __name__ == '__main__':
    app.run(debug=True)
```

### 重定向

```
from flask import Flask,redirect

app = Flask(__name__)

@app.route('/')
def method1():
    return redirect("http://baidu.com")

if __name__ == '__main__':
    app.run(debug=True)
```

### 跳转页面

```
from flask import Flask, render_template, request,redirect

app = Flask(__name__)

@app.route('/login', methods=['GET', 'POST'])
def hello_world():
    print("请求来了")

    # 获取post传过来的值
    user = request.form.get("user")
    pwd = request.form.get("pwd")

    if user == "anthony" and pwd == "123456":
        # return render_template("login.html", **{"msg": "登录成功"})
        return redirect("/index")
    else:
        return render_template("login.html", **{"msg": "用户名或者密码错误"})

@app.route("/index")
def index():
    return "欢迎登录"

if __name__ == '__main__':
    app.run()
```

### 异常捕获

```
from flask import Flask,abort

app = Flask(__name__)

@app.route('/')
def method1():
    """abort 异常抛出"""
    abort(404)
    return  "hello world"

"""捕获异常"""
@app.errorhandler(404)
def method1(e):
    print(e)
    return  "页面找不到"

if __name__ == '__main__':
    app.run(debug=True)
```

### 获取请求参数Form

```python
from flask import Flask, render_template, request

app = Flask(__name__)

@app.route('/login', methods=['GET', 'POST'])
def hello_world():
    print("请求来了")

    # 获取post传过来的值
    user = request.form.get("user")
    pwd = request.form.get("pwd")

    if user == "anthony" and pwd == "123456":
        return render_template("login.html", **{"msg": "登录成功"})
    else:
        return render_template("login.html", **{"msg": "用户名或者密码错误"})

if __name__ == '__main__':
    app.run()
```

### 获取请求参数Body

```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/callback')
def hello_world():
    data = request.get_data()
    print(data)
    return 'Hello, World!'

if __name__ == "__main__":
    app.run(debug=True, port=10086)
```

### 环境变量/配置文件

```python
from flask import Flask,redirect,request

app = Flask(__name__)

# 1.从配置类中加载
class MyConfig(object):
    DEBUG =True
# app.config.from_object(MyConfig)

# 2.从配置文件中加载
# app.config.from_pyfile("Config.ini")
# 在项目的根目录创建个Config.ini文件

# 3.从环境变量
# app.config.from_envvar("")

@app.route('/')
def method1():
    pass

if __name__ == '__main__':
    app.run()
```

### 钩子,类似拦截器

```
from flask import Flask,redirect,request

app = Flask(__name__)

@app.before_first_request
def before_first_request():
    """只请求一次"""
    print("before_first_request")

@app.before_request
def before_request():
    """每次都会请求"""
    print("before_request")

@app.after_request
def after_request(resp):
    """比如做json统一的返回格式"""
    print("after_request")
    return resp

@app.teardown_request
def teardown_request(e):
    """最后会请求到这里,适合做异常信息统计"""
    print("teardown_request")

@app.route('/')
def method1():
    return  "hello world"

if __name__ == '__main__':
    app.run(debug=True)
```

# 视图内容和模板

### 

```
from flask import Flask, make_response, request

app = Flask(__name__)

# 设置cookie
@app.route("/set_cookie")
def set_cookie():
    response = make_response("set cookie")
    response.set_cookie("computer","macbook pro")
    response.set_cookie("age","13 pro",1000)
    return response

@app.route("/get_cookie")
def get_cookie():

    name = request.cookies.get("computer")
    age = request.cookies.get("age")
    return "name:%s,age:%s"%(name,age)

if __name__ == '__main__':
    app.run(debug=True)
```

### Session

```
from flask import Flask, make_response, request, session

app = Flask(__name__)

app.config["SECRET_KEY"]="123456"

# 设置cookie
@app.route("/set_session/<path:name>")
def set_session(name):
    session["name"] =name
    return "set session"

@app.route("/get_session")
def get_session():

    value = session.get("name")
    return "value:%s"%(value)

if __name__ == '__main__':
    app.run(debug=True)
```

# orm

### 入门

```
pip install flask_sqlalchemy
pip install pymysql
```

```
from flask import Flask,render_template
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# 设置数据库的配置信息
app.config["SQLALCHEMY_DATABASE_URI"] = "mysql+pymysql://Rong:Zming1213!@192.168.254.153:3306/data36"
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False

# 创建sqlAlchemy对象,关联app
db = SQLAlchemy(app)

# 创建模型类
class Role(db.Model):
    __tablename__  = "roles"
    id =db.Column(db.Integer,primary_key=True)
    name = db.Column(db.String(32))

class Users(db.Model):
    __tablename__  = "users"
    id =db.Column(db.Integer,primary_key=True)
    name = db.Column(db.String(32))

    # 建立外键
    role_id =db.Column(db.Integer,db.ForeignKey(Role.id))

@app.route("/")
def demo():
    return "response"

if __name__ == '__main__':

    # 创建数据库的表
    db.drop_all()

    db.create_all()

    app.run(debug=True)
```

### 蓝图基本使用

demo01.py

```
from flask import Flask, Blueprint
from demo02 import blue

app = Flask(__name__)

# 讲蓝图注册到app中
app.register_blueprint(blue)

if __name__ == '__main__':
    app.run(debug=True)
```

demo02.py

```
from flask import Blueprint

# 创建蓝图对象
blue = Blueprint("my_blue", __name__)

@blue.route("/")
def demo():
    return "response"

@blue.route("/2")
def demo2():
    return "response2"
```

### 蓝图包的使用

目录结构:|---`demo.py`|----|user||----|user|--`__init__.py`|----|user|--`views.py`

demo.py

```
from flask import Flask

from user import user_blue

app = Flask(__name__)

# 讲蓝图注册到app中
app.register_blueprint(user_blue)

if __name__ == '__main__':
    print(app.url_map)
    app.run(debug=True)
```

**init**.py

```
from flask import Blueprint

# 创建蓝图对象
user_blue = Blueprint("user", __name__)

from user import views
```

views.py

```
from user import user_blue

@user_blue.route("/")
def demo():
    return "response"

@user_blue.route("/2")
def demo2():
    return "response2"
```

# Django

# 命令

```bash
# 安装django
pip install django

# 生成数据库 迁移文件
python manage.py makemigrations

# 执行迁移生成表
python manage.py migrate
```

# PyCharm和Django-admin创建的项目不一样

```bash
# 项目创建
django-admin startproject 项目名

# 结构
项目名
|----|manage.py            [项目管理,启动项目,创建app,数据管理]
|----|项目名同名文件夹
|----|----|__init__.py
|----|----|asgi.py          [项目配置]
|----|----|settings.py      [路由]
|----|----|urls.py          [接收网路请求,异步]
|----|----|wsgi.py          [接收网路请求,同步]

```

```bash
# 用PyCharm创建的目录结构
项目名
|----|manage.py 
|----|templates文件夹
|----|项目名同名文件夹
|----|----|__init__.py
|----|----|asgi.py
|----|----|settings.py
|----|----|urls.py
|----|----|wsgi.py
```

PyCharm创建的根目录下有一个templates目录
Django-admin 是没有的,要在app下的目录创建`templates`

settings.py

```bash
# PyCharm创建的
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates']
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

# 命令行创建的
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': []
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

## 创建应用

```bash
|项目|
|----|app,后台系统[独立的表结构,模板,不相互影响]
|----|app,前台系统[独立的表结构,模板,不相互影响]
```

```bash
# 创建app
python manage.py startapp app名字

djangoProject
├── app01                 [刚创建的app]
│   ├── __init__.py
│   ├── admin.py          [django默认admin后台,不用动]
│   ├── apps.py           [app启动类,不用动]
│   ├── migrations        [数据库的迁移的,数据库变更记录]
│   │   └── __init__.py
│   ├── models.py         [重要,对数据库进行操作]
│   ├── tests.py          [单元测试]
│   └── views.py          [重要,函数]
├── djangoProject
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
└── templates
```

## 注册APP

项目名→项目名同名文件夹→settings.py

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
		# 添加下面一行的配置,下面这个是怎么来的呢?看下面
		'app01.apps.App01Config' 
]
```

项目名→APP文件夹→apps.py

```python
from django.apps import AppConfig

class App01Config(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'app01'
```

## 编写URL和视图函数对应关系

编辑urls.py

![](https://image.runtimes.cc/202404051452032.png)

在app文件夹下的views.py

```python
from django.http import HttpResponse
from django.shortcuts import render

# Create your views here.
# request 必须要的
def index(request):
    return HttpResponse("欢迎使用")
```

## 启动

```bash
# 命令行启动
python ./manage.py runserver
```

## templates模板

![](https://image.runtimes.cc/202404051452463.png)

## 静态资源

```html
djangoProject
├── app01
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── static  [手动创建的]
│   │   ├── css [手动创建的] 
│   │   ├── img [手动创建的]
│   │   │   └── 1.png
│   │   └── js
│   ├── tests.py
│   └── views.py
├── db.sqlite3
├── djangoProject
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
└── templates
    └── user_list.html
```

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>用户列表</title>
</head>
<body>

<h1>用户列表</h1>

加载静态资源
写法一:
<img src="/static/img/1.png">
写法二:
<img src="{% static 'img/1.png' %}">

</body>
</html>
```

## 数据库

主意:app需要先注册

```bash
python ./manage.py makemigrations

python ./manage.py migrate

# Pycharm->tools->Run manager.py Task 开启,可以替代python manager.py 这个前缀
```


```shell
pip3 install pymysql
```

```python
from pymysql import connect

con = connect(host="43.240.30.52",
        port=3306,
        user="yanganli_github",
        password="yanganli_github",
        database="yanganli_github",
        charset="utf8")

cursor = con.cursor()

# 查询出条数
print(cursor.execute("select * from post"))

for one in cursor.fetchall():
    print(one)

cursor.close()
con.close()
```

# 自动化测试

# 安装和命令

```
# 安装pytest
pip install pytest

# 安装python-html包,生成html报告
pip install pytest-html

# 生成allure报告
pip install allure-pytest

# -s 打印print
pytest -s  test_auth.py

# 只测试这一个文件
pytest -q -s  test_auth.py
```

# Pycharm识别pytest

![](https://image.runtimes.cc/202404051452441.png)

# 规范

测试文件已test_开头

测试类已Test开头,并且不能带有init方法

测试函数以test_开头

断言基本使用assert

# 生成报告

```
# 生成html
'--html=../report/report.html'

# 生成xml格式的报告
'--junitxml=./report/report.xml'

# 生成allure报告
'--alluredir','../report/reportallure'

# 生成allure报告,在report的文件夹执行
pytest.main(['../test_case/','--alluredir','../report/allure_raw/'])
./allure serve /myitem/autotest/report/allure_raw
```

# Locust

```
pip install locust

# centos需要添加环境变量
# 如果locust在哪个目录,如果python安装在这个目录:/usr/local/bin/python3
ln -s /usr/local/bin/python3/bin/locust /usr/bin/locust

locust --version
```

# 快速入门

新建一个redis.py

```
class QuickstartUser(HttpUser):
    wait_time = between(1, 10)

    @task
    def index_page(self):
        self.client.get("/hello")
        self.client.get("/world")

    @task(3)
    def view_item(self):
        for item_id in range(10):
            self.client.get(f"/item?id={item_id}", name="/item")
            time.sleep(1)

    def on_start(self):
        self.client.post("/login", json={"username": "foo", "password": "bar"})
```

在文件目录,执行

```
locust -f redis.py

# 启动成功后,访问 http://localhost:8089
```

# 写一个Locust文件

Locust文件是一个普通的python文件,唯一的要求就是至少有一个类继承的`User`类

# User

一个User类标识一个用户,Locust会给每个用户模拟一个User实例,公共的属性通常在User类里被定义

### wait_time 属性

用户的wwait_time方法通常作用于执行的任务简直等待多长时间

这里有三个内建的等待时间的函数

- constant 一段固定的时间
- between 最小和最大数之间的随机时间
- constant_pacing 确保任务运行一次最多不超过x秒的适应时间

举个例子,让每个用户在每个任务执行之间等待0.5s到10s

```
from locust import User, task, between

class MyUser(User):
    @task
    def my_task(self):
        print("executing my_task")

    wait_time = between(0.5, 10)
```

也可以直接在你的类里声明wait_time方法,举个例子,下面的User类,会睡眠1s,2s...

```
class MyUser(User):
    last_wait_time = 0

    def wait_time(self):
        self.last_wait_time += 1
        return self.last_wait_time

    ...
```

### weight 属性

如果文件中存在多个用户类，并且在命令行中没有指定用户类，则Locust将生成每个用户类的相同数量。您还可以通过将用户类作为命令行参数传递来指定从同一个locustfile中使用哪些用户类

```
locust -f locust_file.py WebUser MobileUser
```

如果希望模拟某种类型的更多用户，可以在这些类上设置权重属性,比方说,web用户是移动用户的三倍多

```
class WebUser(User):
    weight = 3
    ...

class MobileUser(User):
    weight = 1
    ...
```

### host 属性

host属性是host的URL前缀,通常,当locust启动的时候,在locust的web界面或者在命令上中使用`--hosts`选项中使用

如果一个类声明了声明的host属性,它将在没有使用`--host`命令行或web请求仲使用

### tasks属性

用户类可以使用`@task`装饰器,将任务声明为方法,但是也可以使用下面详细描述的tasks属性来指定任务。

### environment 属性

对用户正在其中运行的环境的引用,使用它影响这环境,或者是运行着在它的容器中,比如去停止运行从任务方法中

```
self.environment.runner.quit()
```

如果运行的是独立的locust实例,它将停止全部,如果是运行在工作节点,它将停止节点

### on_start 和 on_stop 方法

用户 或者是任务集合可以声明``on_start`方法或者`on_stop`方法,用户将调用它自己的`on_start`方法在它将要开始运行的时候,`on_stop`方法,将在停止运行的时候调用,比如TaskSet,`on_start`方法被调用在模拟的用户开始执行任务,`on_stop`方法在停止模拟的用户执行任务的时候调用(或者被`interrupt()`方法调用,或者是用被用杀掉)

# Tasks

当启动负载测试,一个用户实例将被创建,

下载mp4
```python
import os
import time
import requests
from tqdm import tqdm  # 进度条模块


def down_from_url(url, dst):
    # 设置stream=True参数读取大文件
    response = requests.get(url, stream=True)
    # 通过header的content-length属性可以获取文件的总容量
    file_size = int(response.headers['content-length'])
    if os.path.exists(dst):
        # 获取本地已经下载的部分文件的容量，方便继续下载，如果不存在就从头开始下载。
        first_byte = os.path.getsize(dst)
    else:
        first_byte = 0
    # 如果大于或者等于则表示已经下载完成，否则继续
    if first_byte >= file_size:
        return file_size
    header = {"Range": f"bytes={first_byte}-{file_size}"}

    pbar = tqdm(total=file_size, 
							  initial=first_byte, 
								unit='B', 
								unit_scale=True, 
								desc=dst)

    req = requests.get(url, headers=header, stream=True)

    with open(dst, 'ab') as f:
        # 每次读取一个1024个字节
        for chunk in req.iter_content(chunk_size=1024):
            if chunk:
                f.write(chunk)
                pbar.update(1024)
    pbar.close()
    return file_size


if __name__ == '__main__':
    url = input("请输入.mp4格式的视频链接地址，按回车键确认")
    # 根据时间戳生成文件名
    down_from_url(url, str(time.time()) + ".mp4")
```

SSH下载服务器文件
```python
import paramiko


class LinuxFile:

    def __init__(self, ip, port, username, password):
        try:
            self.ip = ip
            self.port = port
            self.username = username
            self.password = password
            self.transport = paramiko.Transport((str(self.ip), int(self.port)))
            self.transport.connect(username=self.username, password=self.password)
            self.sftp = paramiko.SFTPClient.from_transport(self.transport)
        except Exception as e:
            raise e

    def up_file(self, localhost_file, server_file):
        """
        将本地文件上传至服务器
        :param localhost_file: 本地文件路径
        :param server_file: 服务器保存路径
        :return:
        """
        self.sftp.put(localhost_file, server_file)

    def down_file(self, localhost_file, server_file):
        """
        将服务器文件下载至本地
        :param localhost_file: 本地文件路径
        :param server_file: 服务器保存路径
        :return:
        """
        self.sftp.get(localhost_file, server_file)

    def close(self):
        """
        关闭服务器
        :return:
        """
        self.transport.close()

if __name__ == '__main__':
    test = LinuxFile('47.242.218.75', '22', 'root', 'Qwer1234')
    # test.up_file('../2020-10-11_20-21-28.py', '/root/2020-10-11_20-21-28.py')
    test.down_file('/var/log/nginx/access.log','a.log')
```