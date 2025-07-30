---
title: Go
tags:
  - Go
  - 教程
categories:
  - Go
cover: https://image.runtimes.cc/202404051528208.webp
abbrlink: 56435
date: 2022-11-19 18:25:00
updated: 2023-11-23 16:55:12
---

# 入门
## 参考:
[Go英文文档](https://www.golinuxcloud.com/golang-interview-questions-answers/)

## Hello World
```go
// 一定要在main的包下才能运行
// 一个main包下,只能有一个main入口
package main

import "fmt"

func main() {
 fmt.Print("hello world")
}
```

```powershell
# 第一种,编译后运行.exe文件
# 编译
C:\Users\anthony\go\src\awesomeProject\demo\main>go build demo.go

# 运行编译后exe文件
C:\Users\anthony\go\src\awesomeProject\demo\main>demo.exe
hello world

# 第二种,直接运行源代码go run *.go
```

## 标准输入

```go
package main

import "fmt"

func main() {

    var name string
    var age int

    /*
        使用"&"获取score变量的内存地址(即取变量内存地址的运算符)，通过键盘输入为score变量指向的内存地址赋初值。
        fmt.Scan是一个阻塞的函数，如果它获取不到数据就会一直阻塞哟。
        fmt.Scan可以接收多个参数，用户输入参数默认使用空格或者回车换行符分割输入设备传入的参数，直到接收所有的参数为止
    */
    fmt.Scan(&name, &age)
    fmt.Println(name, age)
}
```

```go
package main

import "fmt"

func main() {

    var name string
    var age int

    /*
        和fmt.Scan功能类似，fmt.Scanln也是一个阻塞的函数，如果它获取不到数据就会一直阻塞哟。
        fmt.Scanln也可以接收多个参数，用户输入参数默认使用空格分割输入设备传入的参数，遇到回车换行符就结束接收参数
    */
    fmt.Scanln(&name, &age)
    fmt.Println(name, age)

}
```

```go
package main

import "fmt"

func main() {

    var name string
    var age int

    /*
        和fmt.Scanln功能类似，fmt.Scanf也是一个阻塞的函数，如果它获取不到数据就会一直阻塞哟。
        其实fmt.Scanln和fmt.Scanf可都以接收多个参数，用户输入参数默认使用空格分割输入设备传入的参数，遇到回车换行符就结束接收参数

        唯一区别就是可以格式化用户输入的数据类型,如下所示:
            %s: 表示接收的参数会被转换成一个字符串类型，赋值给变量
            %d: 表示接收的参数会被转换成一个整形类型，赋值给变量

        生产环境中使用fmt.Scanln和fmt.Scanf的情况相对较少，一般使用fmt.Scan的情况较多~
    */
    fmt.Scanf("%s%d", &name, &age)
    fmt.Println(name, age)

}
```

```go
func method_if() {
 reader := bufio.NewReader(os.Stdin)
 str, _ := reader.ReadString('\n')
 fmt.Printf("输入的值是:%s\n", str)
}
```

## 声明变量

基本类型,声明变量,不赋值,就有默认值

```go
// 基本类型有默认值
func main() {
	var age int = 0
	fmt.Println(age)

	var code int
	fmt.Println(code)

	var sex bool
	fmt.Println(sex)
}
```

一次性声明不同类型的变量

```go
func main() {

	var age, name = 10, "anthony"
	println("可以声明不同类型,只能自动推导,不能定义类型:", age, name)

	// 报错,多变量赋值值,不能声明类型
	// var age2 int,name2 string = 10, "anthony"
	// println(age2,name2)

}

func method6() {
 var (
  age  int    = 23
  name string = "anthony"
  sex  bool
 )
 println("使用集合的方式声明变量,可以声明不同类型,并且可以赋值", age, name, sex)
}
```

先声明,后赋值

```go
func main() {
 // 定义变量
 var i int
 // 给i赋值
 i=10
 // 使用变量
 fmt.Print("i=", i)
}
```

类型推导

```go
func method2() {
 var name = "anthony"
 print("类型推导:", name)
}
```

## 常量

常量是一个简单值的标识符，在程序运行时，不会被修改的量

出于习惯,常量用大写,不过小写也没有问题

```go
// 常量的格式定义
const identifier [type] = value
// 显式类型定义
const A string = "abc"
// 隐式类型定义
const B = "abc"
// 多个相同类型的声明可以简写为
const C,D = 1,2
// 多个不同类型的声明可以简写为
const E, F, G = 1, false, "str"
```

### 常量用作枚举

常量可以作为枚举，常量组

```go
func main() {

 const (
  RED   = 1
  BLACK = 2
  WHITE = 3
 )

 const (
  x int = 16
  y
  s = "abc"
  z
 )

 fmt.Println(RED, BLACK, WHITE)
 fmt.Println("常量组中如不指定类型和初始化值，则与上一行非空常量右值相同:", x, y, s, z)
}
```

### 特殊常量:iota

iota，特殊常量，可以认为是一个可以被编译器修改的常量

1. 如果中断iota自增，则必须显式恢复。且后续自增值按行序递增
2. 自增默认是int类型，可以自行进行显示指定类型
3. 数字常量不会分配存储空间，无须像变量那样通过内存寻址来取值，因此无法获取地址

```
func demo() {
 // 报错
 //a = iota

 const (
  a = iota //0
  b        //1
  c        //2
  d = "ha" //独立值，iota += 1
  e        //"ha"   iota += 1
  f = 100  //iota +=1
  g        //100  iota +=1
  h = iota //7,恢复计数
  i        //8
 )
 fmt.Println(a, b, c, d, e, f, g, h, i)
}
```

## 字符串

```go
func main() {
 name := "Hello World"
 for index := range  name {
  fmt.Printf("遍历字符串:%v,%v\n",index,name[index])
	// 字符串长度
	fmt.Println(len(name[index]))
 }
}
```

### 字符串切片

```
var response = "var r = [[\"000001\",\"HXCZHH\",\"华夏成长混合\",\"混合型-偏股\",\"HUAXIACHENGZHANGHUNHE\"]];"
fmt.Println(response[8:len(response)-1])

// 打印
[["000001","HXCZHH","华夏成长混合","混合型-偏股","HUAXIACHENGZHANGHUNHE"]]
```

### 字符串数组转一维数组

```go
func main() {
	response := "[1,2,3]"
	var arr []int
	if err := json.Unmarshal([]byte(response), &arr); err != nil {
		fmt.Println("Error:", err)
	}

  // 第1行;数据是:1
	// 第2行;数据是:2
	// 第3行;数据是:3
	for i:=0 ;i< len(arr);i++ {
		fmt.Printf("第%v行;数据是:%v\n",i+1,arr[i])
	}
}
```

### 字符串数组转二维数组

```go
func main() {
	response := "[[1,2,3],[2,3,4]]"
	var arr [][]int
	if err := json.Unmarshal([]byte(response), &arr); err != nil {
		fmt.Println("Error:", err)
	}

    // 第1行;数据是:[1 2 3]
	// 第2行;数据是:[2 3 4]
	for i:=0 ;i< len(arr);i++ {
		fmt.Printf("第%v行;数据是:%v\n",i+1,arr[i])
	}
}
```

### 字符串和数字互转

```go
string转成int：int, err := strconv.Atoi(string)

string转成int64：int64, err := strconv.ParseInt(string, 10, 64)

int转成string：string := strconv.Itoa(int)

int64转成string：string := strconv.FormatInt(int64,10)
```

## type关键字

```go
func main() {

 // 类型转换
 var one int = 17
 mean := float32(one)
 fmt.Println(mean)

 // 字符串不能强转整型

}
```

## 流程控制
```go
// if
func method_if() {

	 reader := bufio.NewReader(os.Stdin)
	 str, _ := reader.ReadString('\n')
	
	 if str == "" {
	  fmt.Println("空的")
	 }
	
	 fmt.Printf("输入的值是:%s\n", str)

}

// if-ik
func main() {

	var data = make(map[string]int)
	data["a"] = 0
	data["b"] = 1
	data["c"] = 2

	// 遍历
	for k, v := range data {
		fmt.Println(k, v)
	}

	if _, ok := data["a"]; ok {
		//存在
		fmt.Println("存在")
	}

}

// if-else
func method_if2() {

 if num := 10; num%2 == 0 { //checks if number is even
  fmt.Println(num, "is even")
 } else {
  fmt.Println(num, "is odd")
 }

}

// switch
func method_switch() {

 num := 1
 result := -1

 switch num {
 case 1:
  result = 1
 case 2, 3, 4:
  result = 2
 default:
  result = -2

 }

 fmt.Println(result)

}

// for  开始012345678910结束
func demo() {
 fmt.Print("开始")
 for i := 0; i <= 10; i++ {
  fmt.Print(i)
 }
 fmt.Print("结束")
}

// break 开始01234结束
func demo_break() {

 fmt.Print("开始")
 for i := 0; i <= 10; i++ {
  if i == 5 {
   break
  }
  fmt.Print(i)
 }

 fmt.Print("结束")
}

// coutinue   1 3 5 7 9
func demo_continue() {

 for i := 1; i <= 10; i++ {
  if i%2 == 0 {
   continue
  }
  fmt.Printf("%d ", i)
 }
}

// go-to
// go
// 会打印两次 ===
func demo_goto() {

 /* 定义局部变量 */
 var a = 0

 /* 循环 */
LOOP:
 fmt.Println("===")
 for a < 10 {
  if a == 5 {
   /* 跳过迭代 */
   a = a + 1
   goto LOOP
  }
  fmt.Printf("a的值为 : %d\n", a)
  a++
 }
}
```

```

```

## 值传递和引用传递

程序中使用的是值传递, 所以两个值并没有实现交互

```go
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int = 200

   fmt.Printf("交换前 a 的值为 : %d\n", a )
   fmt.Printf("交换前 b 的值为 : %d\n", b )

   /* 通过调用函数来交换值 */
   swap(a, b)

   fmt.Printf("交换后 a 的值 : %d\n", a )
   fmt.Printf("交换后 b 的值 : %d\n", b )
}

/* 定义相互交换值的函数 */
func swap(x, y int) int {
   var temp int

   temp = x /* 保存 x 的值 */
   x = y    /* 将 y 值赋给 x */
   y = temp /* 将 temp 值赋给 y*/

   return temp;
}

//交换前 a 的值为 : 100
//交换前 b 的值为 : 200
//交换后 a 的值 : 100
//交换后 b 的值 : 200
```

引用传递

```go
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int= 200

   fmt.Printf("交换前，a 的值 : %d\n", a )
   fmt.Printf("交换前，b 的值 : %d\n", b )

   /* 调用 swap() 函数
   * &a 指向 a 指针，a 变量的地址
   * &b 指向 b 指针，b 变量的地址
   */
   swap(&a, &b)

   fmt.Printf("交换后，a 的值 : %d\n", a )
   fmt.Printf("交换后，b 的值 : %d\n", b )
}

func swap(x *int, y *int) {
   var temp int
   temp = *x    /* 保存 x 地址上的值 */
   *x = *y      /* 将 y 值赋给 x */
   *y = temp    /* 将 temp 值赋给 y */
}

//交换前，a 的值 : 100
//交换前，b 的值 : 200
//交换后，a 的值 : 200
//交换后，b 的值 : 100
```

## 变量作用域

```go
// 全局变量
var global int = 32

func main() {

 // 局部变量
 var a,b int =1, 2

 fmt.Printf("打印全局变变量:%v\n", global)
 global = a+ b
 fmt.Printf("打印全局变变量:%v\n", global)

 var global int = 3
 fmt.Printf("打印全局变变量:%v\n", global)

}
```

匿名代码块

匿名代码块被用来定义一个局部变量 `message`，该变量仅在代码块内部可见。一旦离开代码块，变量 `message` 就不再可见，任何尝试在代码块外部访问它都会导致编译错误。

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("在main函数外部")

	// 进入匿名代码块
	{
		fmt.Println("在匿名代码块内部")
		// 定义一个局部变量，仅在代码块内可见
		message := "这是一个局部变量"
		fmt.Println(message)
	}

	// message 变量在代码块外不可见
	// fmt.Println(message)  // 这会导致编译错误

	fmt.Println("在main函数外部")
}

```

## 数组和切片

数组
```go
func get() {

	list := [5]int{1,2,3,4,5}

	for i:=0;i< len(list);i++ {
		fmt.Println(i, "==", list[i])
	}

	for k, v := range my {
		fmt.Println(k, v)
	}
}

func create(){

 var numbers []int
 fmt.Println("新建个空数组:",numbers)

 var defaultcount [4]int
 fmt.Println("新建个指定长度的数组:",defaultcount)

 var balance =  []int{1,2,3,4,5}
 fmt.Println("新建个不指定长度的数组:",balance)

 // 根据元素的个数，设置数组的大小
 d := [...] int{1,2,3,4,5}
 fmt.Println("新建个指定位置的数组:",d)

 // 指定位置
 e := [5] int{4: 100} // [0 0 0 0 100]
 fmt.Println("新建个指定位置的数组:",e)

 // 指定位置
 f := [...] int{0: 1, 4: 1, 9: 1} // [1 0 0 0 1 0 0 0 0 1]
 fmt.Println("新建个指定位置的数组:",f)

}
```

切片
```go
func define() {

	var identifier []int
	fmt.Println("空数组", identifier)

	var slice1 []int = make([]int, 10)
	fmt.Println("切片", slice1)

	i0 := slice1[0]
	i1 := slice1[1]
	i2 := slice1[2]
	i3 := slice1[3]
	fmt.Println("通过索引,获取值", i0, i1, i2, i3)

	// 修改切片
	for i := 0; i < len(slice1); i++ {
		slice1[i] = i
	}
	fmt.Println("修改完切片::", slice1)

	fmt.Println("获取切片区间1:", slice1[0:2])
	fmt.Println("获取切片区间2:", slice1[5:])
	fmt.Println("获取切片区间3:", slice1[:5])

	slice1 = append(slice1, 10, 11, 12)
	fmt.Println("追加完切片::", slice1)

	slice2 := make([]int, len(slice1), cap(slice1)*2)
	fmt.Println("创建个容量是原来容量两位的数组:", slice2)

	number := copy(slice2, slice1)
	fmt.Printf("slice:%v,slice2:%v,number:%v:", slice1, slice2, number)

}
```

## Map

```go
func main() {

	keyvale := make(map[string]string)
	keyvale["k1"] = "v1"
	keyvale["k2"] = "v2"
	keyvale["k3"] = "v3"
	keyvale["k4"] = "v4"

	// 循环遍历
	for key := range keyvale {
		fmt.Println("循环遍历:", key, keyvale[key])
	}

	// 删除元素
	delete(keyvale, "k1")
	for key := range keyvale {
		fmt.Println("删除值之后,循环遍历:", key, keyvale[key])
	}

	// 查看元素是否存在
	_, ok := keyvale["United States"]
	if ok {
		fmt.Printf("存在\n")
	} else {
		fmt.Printf("不存在\n")
	}

	// 但是当key如果不存在的时候，我们会得到该value值类型的默认值，比如string类型得到空字符串，int类型得到0。但是程序不会报错
	m := make(map[string]int)
	m["a"] = 1
	x, ok := m["b"]
	fmt.Println("key不存在时,会有默认值", x, ok)

	// map长度
	fmt.Println("map长度", len(m))

	// map是引用类型
	mymap := map[string]int{
		"steven":  12000,
		"anthony": 15000,
	}
	mymap["mike"] = 9000
	fmt.Printf("原来的数据:%v\n", mymap)

	newmymap := mymap
	newmymap["anthony"] = 50000
	fmt.Printf("改变后,原来的数据:%v\n", mymap)
	fmt.Printf("改变后,新的的数据:%v\n", newmymap)
}
```

## 函数

```go
// 函数返回两个数的最大值
func max(num1, num2 int) int {
   /* 声明局部变量 */
   var result int

   if (num1 > num2) {
      result = num1
   } else {
      result = num2
   }
   return result
}

// 函数调用
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int = 200
   var ret int

   /* 调用函数并返回最大值 */
   ret = max(a, b)

   fmt.Printf( "最大值是 : %d\n", ret )
}
```

### 函数返回多个值

```go
// 入参多个值,x和y 都是字符串
// 返回值是多个,都是字符串
func swap(x, y string) (string, string) {
   return y, x
}

func main() {
   a, b := swap("Google", "Runoob")
   fmt.Println(a, b)
}
```

### 函数返回值的命名

```go
type studen struct {
	name string
	age  int
}

func test4() {
	stu := test4_return()
	fmt.Println(stu.name)
}

func test4_return() (stu *studen) {

	stu = &studen{
		"anthony",
		13,
	}
	return
}
```

### 函数作为实参

```go
type fb func(x int) int

func main() {

   myfunc := func(x int) int{
      return x
   }

    // 知识点1
 fmt.Println(myfunc(3))

    // 知识点2
 demo(2, myfunc)
}

// 函数作为参数传递，实现回调
func demo(x int,myfb fb)  {
 myfb(x)
}
```

### 函数作为实参-回调

```go
func main() {
	test2()
}

func test2() {
	group("/system1", myfunc)

	group("/system2", func(path2 string) {
		fmt.Printf("回调Path:%v \n", path2)
	})
}

func group(path string, group func(path2 string)) {
	fmt.Printf("手动实现 \n" + path)
	group(path)
}

func myfunc(path2 string) {
	fmt.Printf("回调Path:%v \n", path2)
}
```

### 函数作为参数-interface{}

```go
func test3() {
	test3_object(myfunc)
}

func test3_object(object interface{}) {
	a := object
	fmt.Printf("什么鬼:%v", a)
}
```

## 指针

![](https://image.runtimes.cc/202404051522806.png)

```go
func main() {
   var a int= 20   /* 声明实际变量 */
   var ip *int        /* 声明指针变量 */

   ip = &a  /* 指针变量的存储地址 */

   fmt.Printf("a 变量的地址是: %x\n", &a  )

   /* 指针变量的存储地址 */
   fmt.Printf("ip 变量储存的指针地址: %x\n", ip )

   /* 使用指针访问值 */
   fmt.Printf("*ip 变量的值: %d\n", *ip )
}

// 函数使用指针
func main() {
    /* 定义局部变量 */
   var a int = 100
   var b int= 200
   swap(&a, &b);

   fmt.Printf("交换后 a 的值 : %d\n", a )
   fmt.Printf("交换后 b 的值 : %d\n", b )
}

/* 交换函数这样写更加简洁，也是 go 语言的特性，可以用下，c++ 和 c# 是不能这么干的 */

func swap(x *int, y *int){
    *x, *y = *y, *x
}
```

## 结构体

```go
func main() {
	// 返回的是该实例的结构类型
	var s people
	s.age = 1
	fmt.Println(s)

	// 第二第三种，返回的是一个指向这个结构类型的一个指针，是地址
	ming := new(people)
	ming.name = "xiao ming"
	ming.age = 18
	fmt.Println(ming)

	ming2 := &people{"xiao ming", 18}
	fmt.Println(ming2)
}
```

第二第三种返回指针的声明形式，在我们需要修改他的值的时候，其实应该使用的方式是:

```go
(*ming).name = "xiao wang"
```

也就是说，对于指针类型的数值，应该要先用`*`取值，然后再修改。

但是，在Golang中，可以省略这一步骤，直接使用`ming.name = "xiao wang"`。

### 方法

```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) test1() {
	v.X++
	v.Y++
}

func (v *Vertex) test2() {
	v.X++
	v.Y++
}

func main() {
	v1 := Vertex{1, 1}
	v2 := &Vertex{1, 1}

	v1.test1()
	v2.test2()

	// {1 1}
	fmt.Println(v1)
	// &{2 2}
	fmt.Println(v2)
}
```

`test1`和`test2`，他们唯一的区别就是方法名前面的接收者不同，一个是指针类型的，一个是值类型的。

### 测试

```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) test1(){
	// 自己定义的v1内存地址为：0xc00000a0e0
	fmt.Printf("在方法中的v的地址为：%p\n", &v)
	v.X++;
	v.Y++;
}

func main()  {
	v1 := Vertex{1, 1}
	// 在方法中的v的地址为：0xc00000a100
	fmt.Printf("自己定义的v1内存地址为：%p\n", &v1)
	v1.test1()
}
```

```go
type Vertex struct {
	X, Y float64
}

func (v *Vertex) test2(){
	// 自己定义的v1内存地址为：0xc00000a0e0
	fmt.Printf("在方法中的v的地址为：%p\n", v)
	v.X++;
	v.Y++;
}

func main()  {
	v1 := &Vertex{1, 1}
	// 
	fmt.Printf("自己定义的v1内存地址为：%p\n", v1)
	v1.test2()
}
```

```go
type Books struct {
 title string
 author string
 subject string
 book_id int
}

type Library struct {
 // 匿名字段，那么默认Student就包含了Human的所有字段
 Books

 address string
}

func main() {
 var book Books
 book.title = "Go 语言"
 book.author = "www.runoob.com"
 book.subject = "Go 语言教程"
 book.book_id = 6495407

 // 初始化一个图书馆
 mark := Library{Books{"Go 语言","www.runoob.com","Go 语言教程",6495407},"广东"}
 // 我们访问相应的字段
 fmt.Println("His name is ", mark.title)
 fmt.Println("His age is ", mark.author)
 fmt.Println("His weight is ", mark.subject)
 fmt.Println("His speciality is ", mark.address)
 // 修改对应的备注信息
 mark.title = "AI"
 fmt.Println("Mark changed his speciality")
 fmt.Println("His speciality is ", mark.title)
}
```

## 面向对象

```go
// 定义接口
type PersonMethod interface {
 show()
}

// 定义结构体
type Person struct {
 name string
 age  int
}

// 对象成员方法
func (person *Person) setAge(age int) {
 person.age = age
}

// 对象成员方法实现接口
func (person Person) show() {
 fmt.Printf("Person类打印SHOW:%v\n", person)
}

// 多态
func show2(person PersonMethod) {
 person.show()
}

// 学生类,继承Person类
type Student struct {
 // 匿名,相当于继承
 Person
 level string
}

func (student *Student) show() {
 fmt.Printf("Student类打印SHOW:%v\n", student)
}

// 老师类,继承Person类
type Teacher struct {
 Person
 price int
}

func (teacher Teacher) show() {
 fmt.Printf("Teacher类打印SHOW:%v\n", teacher)
}

func main() {
 anthony := Person{"anthony", 25}
 fmt.Printf("anthony信息:%v\n", anthony)

 // 调用成员方法
 anthony.setAge(12)
 fmt.Printf("anthony信息:%v\n", anthony)

 anthony2 := Person{}
 fmt.Printf("anthony2信息:%v\n", anthony2)

 anthony2.age = 26
 anthony2.name = "anthony2"
 fmt.Printf("anthony2信息:%v\n", anthony2)

 // 学生,继承
 student := Student{}
 student.level = "小学生"
 student.name = "anthony"
 student.age = 23
 fmt.Printf("学生继承类:%v\n", student)

 // 老师,继承
 teacher := Teacher{price: 12, Person: Person{name: "li teacher", age: 56}}
 fmt.Printf("老师继承类:%v\n", teacher)

 show2(&student)
 show2(teacher)
 show2(anthony)

}
```

创建对象的方式

```go

// 方式一：使用T{…}方式，结果为值类型
c := Car{}

// 方式二：使用new的方式，结果为指针类型
c1 := new(Car)

// 方式三：使用&方式，结果为指针类型
c2 := &Car{}

// 以下为创建并初始化
c3 := &Car{"红色", "1.2L"}
c4 := &Car{color: "红色"}
c5 := Car{color: "红色"}

// 没有构造函数的概念，对象的创建通常交由一个全局的创建函数来完成，以NewXXX 来命名，表示“构造函数” ：
func NewCar(color,size string)*Car  {
	return &Car{color,size}
}
```

## 接口

```go
type Phone interface {
    call()
}

type NokiaPhone struct {
}

func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}

func main() {
    var phone Phone

    phone = new(NokiaPhone)
    phone.call()

    phone = new(IPhone)
    phone.call()

}
```

## 错误处理

```go
// 自定义错误信息结构
type DivErr struct {
 etype int  // 错误类型
 v1 int     // 记录下出错时的除数、被除数
 v2 int
}

// 实现接口方法 error.Error()
func (divErr DivErr) Error() string {
 if 0== divErr.etype {
  return "除零错误"
 }else{
  return "其他未知错误"
 }
}

// 除法
func div(a int, b int) (int,*DivErr) {
 if b == 0 {
  // 返回错误信息
  return 0,&DivErr{0,a,b}
 } else {
  // 返回正确的商
  return a / b, nil
 }
}

func main() {

 // 正确调用
 v,r :=div(100,2)
 if nil!=r{
  fmt.Println("(1)fail:",r)
 }else{
  fmt.Println("(1)succeed:",v)
 }
 // 错误调用
 v,r =div(100,0)
 if nil!=r{
  fmt.Println("(2)fail:",r)
 }else{
  fmt.Println("(2)succeed:",v)
 }

}
```

## GoRoutine

```go
func main() {
 go say("world")
 say("hello")
}

func say(s string) {
 for i := 0; i < 5; i++ {
  time.Sleep(100 * time.Millisecond)
  fmt.Println(s)
 }
}
```

## Channel

```go
package main

import "fmt"

func main() {
   
   messages := make(chan string)

   go func() { messages <- "ping" }()

   msg := <-messages
   fmt.Println(msg)
}
```

## 包管理器

### modules

go mod常用命令

| 命令     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| download | download modules to local cache(下载依赖包)                  |
| edit     | edit go.mod from tools or scripts（编辑go.mod                |
| graph    | print module requirement graph (打印模块依赖图)              |
| init     | initialize new module in current directory（在当前目录初始化mod） |
| tidy     | add missing and remove unused modules(拉取缺少的模块，移除不用的模块) |
| vendor   | make vendored copy of dependencies(将依赖复制到vendor下)     |
| verify   | verify dependencies have expected content (验证依赖是否正确） |
| why      | explain why packages or modules are needed(解释为什么需要依赖) |

### 示例一：创建一个新项目

1. 在`GOPATH 目录之外`新建一个目录，并使用`go mod init` 初始化生成`go.mod` 文件

go.mod文件一旦创建后，它的内容将会被go toolchain全面掌控。go toolchain会在各类命令执行时，比如go get、go build、go mod等修改和维护go.mod文件。

go.mod 提供了`module`, `require`、`replace`和`exclude` 四个命令

- `module` 语句指定包的名字（路径）
- `require` 语句指定的依赖项模块
- `replace` 语句可以替换依赖项模块
- `exclude` 语句可以忽略依赖项模块
1. 添加依赖

新建一个 server.go 文件，写入以下代码：

```go
package main

import (
 "net/http"

 "github.com/labstack/echo"
)

func main() {
 e := echo.New()
 e.GET("/", func(c echo.Context) error {
  return c.String(http.StatusOK, "Hello, World!")
 })
 e.Logger.Fatal(e.Start(":1323"))
}
```

执行 `go run server.go` 运行代码会发现 go mod 会自动查找依赖自动下载：

```go
$ go run server.go
go: finding github.com/labstack/echo v3.3.10+incompatible
go: downloading github.com/labstack/echo v3.3.10+incompatible
go: extracting github.com/labstack/echo v3.3.10+incompatible
go: finding github.com/labstack/gommon/color latest
go: finding github.com/labstack/gommon/log latest
go: finding github.com/labstack/gommon v0.2.8
# 此处省略很多行
...

   ____    __
  / __/___/ /  ___
 / _// __/ _ \/ _ \
/___/\__/_//_/\___/ v3.3.10-dev
High performance, minimalist Go web framework
https://echo.labstack.com
____________________________________O/_______
                                    O\
⇨ http server started on [::]:1323
复制代码
```

现在查看go.mod 内容：

```go
$ cat go.mod

module hello

go 1.12

require (
 github.com/labstack/echo v3.3.10+incompatible // indirect
 github.com/labstack/gommon v0.2.8 // indirect
 github.com/mattn/go-colorable v0.1.1 // indirect
 github.com/mattn/go-isatty v0.0.7 // indirect
 github.com/valyala/fasttemplate v1.0.0 // indirect
 golang.org/x/crypto v0.0.0-20190313024323-a1f597ede03a // indirect
)
复制代码
```

go module 安装 package 的原則是先拉最新的 release tag，若无tag则拉最新的commit，详见 [Modules官方介绍](https://github.com/golang/go/wiki/Modules)。 go 会自动生成一个 go.sum 文件来记录 dependency tree：

```go
$ cat go.sum
github.com/labstack/echo v3.3.10+incompatible h1:pGRcYk231ExFAyoAjAfD85kQzRJCRI8bbnE7CX5OEgg=
github.com/labstack/echo v3.3.10+incompatible/go.mod h1:0INS7j/VjnFxD4E2wkz67b8cVwCLbBmJyDaka6Cmk1s=
github.com/labstack/gommon v0.2.8 h1:JvRqmeZcfrHC5u6uVleB4NxxNbzx6gpbJiQknDbKQu0=
github.com/labstack/gommon v0.2.8/go.mod h1:/tj9csK2iPSBvn+3NLM9e52usepMtrd5ilFYA+wQNJ4=
github.com/mattn/go-colorable v0.1.1 h1:G1f5SKeVxmagw/IyvzvtZE4Gybcc4Tr1tf7I8z0XgOg=
github.com/mattn/go-colorable v0.1.1/go.mod h1:FuOcm+DKB9mbwrcAfNl7/TZVBZ6rcnceauSikq3lYCQ=
... 省略很多行
复制代码
```

1. 再次执行脚本 `go run server.go` 发现跳过了检查并安装依赖的步骤。
2. 可以使用命令 `go list -m -u all` 来检查可以升级的package，使用`go get -u need-upgrade-package` 升级后会将新的依赖版本更新到go.mod * 也可以使用 `go get -u` 升级所有依赖

#### go get 升级

- 运行 go get -u 将会升级到最新的次要版本或者修订版本(x.y.z, z是修订版本号， y是次要版本号)
- 运行 go get -u=patch 将会升级到最新的修订版本
- 运行 go get package[@version](notion://www.notion.so/version)  将会升级到指定的版本号version
- 运行go get如果有版本的更改，那么go.mod文件也会更改

### 示例二：改造现有项目(helloword)

项目目录为：

```shell
$ tree
.
├── api
│   └── apis.go
└── server.go

1 directory, 2 files
```

server.go 源码为：

```go
package main

import (
    api "./api"  // 这里使用的是相对路径
    "github.com/labstack/echo"
)

func main() {
    e := echo.New()
    e.GET("/", api.HelloWorld)
    e.Logger.Fatal(e.Start(":1323"))
}
复制代码
```

api/apis.go 源码为：

```go
package api

import (
    "net/http"

    "github.com/labstack/echo"
)

func HelloWorld(c echo.Context) error {
    return c.JSON(http.StatusOK, "hello world")
}
复制代码
```

1. 使用 `go mod init ***` 初始化go.mod

```
$ go mod init helloworld
go: creating new go.mod: module helloworld
复制代码
```

1. 运行 `go run server.go`

```shell
go: finding github.com/labstack/gommon/color latest
go: finding github.com/labstack/gommon/log latest
go: finding golang.org/x/crypto/acme/autocert latest
go: finding golang.org/x/crypto/acme latest
go: finding golang.org/x/crypto latest
build command-line-arguments: cannot find module for path _/home/gs/helloworld/api
```

首先还是会查找并下载安装依赖，然后运行脚本 `server.go`，这里会抛出一个错误：

```shell
build command-line-arguments: cannot find module for path _/home/gs/helloworld/api
```

但是`go.mod` 已经更新：

```bash
$ cat go.mod
module helloworld

go 1.12

require (
        github.com/labstack/echo v3.3.10+incompatible // indirect
        github.com/labstack/gommon v0.2.8 // indirect
        github.com/mattn/go-colorable v0.1.1 // indirect
        github.com/mattn/go-isatty v0.0.7 // indirect
        github.com/valyala/fasttemplate v1.0.0 // indirect
        golang.org/x/crypto v0.0.0-20190313024323-a1f597ede03a // indirect
)
```

### 那为什么会抛出这个错误呢？

这是因为 server.go 中使用 internal package 的方法跟以前已经不同了，由于 go.mod会扫描同工作目录下所有 package 并且`变更引入方法`，必须将 helloworld当成路径的前缀，也就是需要写成 import helloworld/api，以往 GOPATH/dep 模式允许的 import ./api 已经失效，详情可以查看这个 [issue](https://github.com/golang/go/issues/26645)。

1. 更新旧的package import 方式

所以server.go 需要改写成：

```
package main

import (
    api "helloworld/api"  // 这是更新后的引入方法
    "github.com/labstack/echo"
)

func main() {
    e := echo.New()
    e.GET("/", api.HelloWorld)
    e.Logger.Fatal(e.Start(":1323"))
}
复制代码
```

`一个小坑`：开始在golang1.11 下使用go mod 遇到过 `go build github.com/valyala/fasttemplate: module requires go 1.12` [这种错误](https://github.com/golang/go/issues/27565)，遇到类似这种需要升级到1.12 的问题，直接升级golang1.12 就好了。幸亏是在1.12 发布后才尝试的`go mod` 🤷‍♂️

# 网络编程

## 最简单的网络编程

```go
package main

import (
 "io"
 "log"
 "net/http"
)

func HelloServer(w http.ResponseWriter, req *http.Request) {
 io.WriteString(w, "hello, world!\n")
}
func main() {
 http.HandleFunc("/hello", HelloServer)
 err := http.ListenAndServeTLS(":8080", "cert.pem", "key.pem", nil)
 if err != nil {
  log.Fatal("ListenAndServe: ", err)
 }
}
```

## TLS服务端和客户端

serve.go

```go
package main

import (
 "bufio"
 "crypto/tls"
 "log"
 "net"
)
func main() {
 log.SetFlags(log.Lshortfile)
 key, err := tls.LoadX509KeyPair("cert.pem", "key.pem")
 if err != nil {
  log.Println(err)
  return
 }
 config := &tls.Config{Certificates: []tls.Certificate{key}}
 ln, err := tls.Listen("tcp", ":8000", config)
 if err != nil {
  log.Println(err)
  return
 }
 defer ln.Close()
 for {
  conn, err := ln.Accept()
  if err != nil {
   log.Println(err)
   continue
  }
  go handleConnection(conn)
 }
}
func handleConnection(conn net.Conn) {
 defer conn.Close()
 r := bufio.NewReader(conn)
 for {
  msg, err := r.ReadString('\n')
  if err != nil {
   log.Println(err)
   return
  }
  println(msg)
  n, err := conn.Write([]byte("world\n"))
  if err != nil {
   log.Println(n, err)
   return
  }
 }
}
```

clinet.go

```go
package main

import (
 "crypto/tls"
 "log"
)
func main() {
 log.SetFlags(log.Lshortfile)
 conf := &tls.Config{
  InsecureSkipVerify: true,
 }
 conn, err := tls.Dial("tcp", "127.0.0.1:8000", conf)
 if err != nil {
  log.Println(err)
  return
 }
 defer conn.Close()
 n, err := conn.Write([]byte("hello\n"))
 if err != nil {
  log.Println(n, err)
  return
 }
 buf := make([]byte, 100)
 n, err = conn.Read(buf)
 if err != nil {
  log.Println(n, err)
  return
 }
 println(string(buf[:n]))
}
```

# 标准库

## flag

```go
import (
 "errors"
 "flag"
 "fmt"
 "strings"
 "time"
)

/**

// 编译
go build demo.go

// 帮助信息
demo.exe -h

// 正常使用
demo.exe -name yang
*/
func main() {

 //demo1()

 demo2()

}

// 第一种用法
func demo1() {
 var nFlag = flag.String("name", "anthony", "help message for flag n")
 flag.Parse()
 fmt.Println(*nFlag)
}

// 第二种用法
func demo2() {
 var name string
 flag.StringVar(&name, "name", "anthonyyang", "help message for flag n")
 flag.Parse()
 fmt.Println(name)
}

// 第三种用法
func demo3() {
 var intervalFlag mystring
 flag.Var(&intervalFlag, "anthonyyang", "name")
 fmt.Println(intervalFlag)
}

type mystring []time.Duration

func (i *mystring) String() string {
 return fmt.Sprint()
}

func (i *mystring) Set(value string) error {
 if len(*i) > 0 {
  return errors.New("interval flag already set")
 }
 for _, dt := range strings.Split(value, ",") {
  duration, err := time.ParseDuration(dt)
  if err != nil {
   return err
  }
  *i = append(*i, duration)
 }
 return nil
}
```

[参考](https://darjun.github.io/2020/01/10/godailylib/flag/)

## log

## io

### io.Reader

对于要用作读取器的类型，它必须实现 `io.Reader` 接口的唯一一个方法 `Read(p []byte)`。换句话说，只要实现了 `Read(p []byte)` ，那它就是一个读取器。

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
```

`Read()` 方法有两个返回值，一个是读取到的字节数，一个是发生错误时的错误。同时，如果资源内容已全部读取完毕，应该返回 `io.EOF` 错误。

### io.Copy

## sync

### sync.WaitGroup

```go
import (
    "fmt"
)

// 执行代码很可能看不到输出，因为有可能这两个协程还没得到执行主协程已经结束了，而主协程结束时会结束所有其他协程
func main() {
    go func() {
        fmt.Println("Goroutine 1")
    }()

    go func() {
        fmt.Println("Goroutine 2")
    }()
}
```

解决办法:

```go
import (
    "fmt"
    "time"
)

// 在main函数结尾加上等待
func main() {
    go func() {
        fmt.Println("Goroutine 1")
    }()

    go func() {
        fmt.Println("Goroutine 2")
    }()

    time.Sleep(time.Second * 1) // 睡眠1秒，等待上面两个协程结束
}
```

问题来了,不确定协程到底要用运行多长时间,比如协程需要用到2s,这里`main`等待1s,就不够用了

解决办法:用管道

```go
import (
    "fmt"
)

func main() {

    ch := make(chan struct{})
    count := 2 // count 表示活动的协程个数

    go func() {
        fmt.Println("Goroutine 1")
        ch <- struct{}{} // 协程结束，发出信号
    }()

    go func() {
        fmt.Println("Goroutine 2")
        ch <- struct{}{} // 协程结束，发出信号
    }()

    for range ch {
        // 每次从ch中接收数据，表明一个活动的协程结束
        count--
        // 当所有活动的协程都结束时，关闭管道
        if count == 0 {
            close(ch)
        }
    }
}
```

虽然比较完美的方案，但是Go提供了更简单的方法——使用sync.WaitGroup

WaitGroup顾名思义，就是用来等待一组操作完成的。

WaitGroup内部实现了一个计数器，用来记录未完成的操作个数，它提供了三个方法，Add()用来添加计数。

Done()用来在操作结束时调用，使计数减一。Wait()用来等待所有的操作结束，即计数变为0，该函数会在计数不为0时等待，在计数为0时立即返回

```go
import (
    "fmt"
    "sync"
)

func main() {

    var wg sync.WaitGroup

    wg.Add(2) // 因为有两个动作，所以增加2个计数
    go func() {
        fmt.Println("Goroutine 1")
        wg.Done() // 操作完成，减少一个计数
    }()

    go func() {
        fmt.Println("Goroutine 2")
        wg.Done() // 操作完成，减少一个计数
    }()

    wg.Wait() // 等待，直到计数为0
}
```

## net

### http

#### Get 
不带参数

```go
func main() {
	resp, err := http.Get("http://fund.eastmoney.com/js/fundcode_search.js")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer resp.Body.Close()
	body, err := ioutil.ReadAll(resp.Body)
    // 打印body
	fmt.Println(string(body))
    // 打印状态码
	fmt.Println(resp.StatusCode)
	if resp.StatusCode == 200 {
		fmt.Println("ok")
		parse(string(body))
	}

}
```

### net.Listen

Listen通知本地网络地址。

网络必须是“tcp”，“tcp4”，“tcp6”，“unix”或“unixpacket”。

对于TCP网络，如果地址参数中的主机为空或文字未指定的IP地址，则Listen会监听本地系统的所有可用单播和任播IP地址。

要仅使用IPv4，请使用网络“tcp4”。

该地址可以使用主机名称，但不建议这样做，因为它将为主机的至多一个IP地址创建一个监听器。

如果地址参数中的端口为空或“0”，如“127.0.0.1：”或“:: 1：0”中所示，则会自动选择一个端口号。Listener的Addr方法可用于发现所选端口。

```
for retry := 0; retry < 16; retry++ {
    l, err := net.Listen("tcp", host+":0")
    if err != nil {
        continue
    }
    defer l.Close()
    _, port, err := net.SplitHostPort(l.Addr().String())
    p, err := strconv.ParseInt(port, 10, 32)
    return int(p)
}
```

### net.Conn.SetReadDeadline

设置超时时间

`net.Conn`为Deadline提供了多个方法`Set[Read|Write]Deadline(time.Time)`。

Deadline是一个绝对时间值，当到达这个时间的时候，所有的 I/O 操作都会失败，返回超时(timeout)错误。

## OS

### Stat

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    fileinfo, err := os.Stat(`C:\Users\Administrator\Desktop\UninstallTool.zip`)
    if err != nil {
        panic(err)
    }
    fmt.Println(fileinfo.Name())    //获取文件名
    fmt.Println(fileinfo.IsDir())   //判断是否是目录，返回bool类型
    fmt.Println(fileinfo.ModTime()) //获取文件修改时间
    fmt.Println(fileinfo.Mode())
    fmt.Println(fileinfo.Size()) //获取文件大小
    fmt.Println(fileinfo.Sys())
}

type FileInfo interface {
    Name() string       // 文件的名字（不含扩展名）
    Size() int64        // 普通文件返回值表示其大小；其他文件的返回值含义各系统不同
    Mode() FileMode     // 文件的模式位
    ModTime() time.Time // 文件的修改时间
    IsDir() bool        // 等价于Mode().IsDir()
    Sys() interface{}   // 底层数据来源（可以返回nil）
}
```

### IsExist

返回一个布尔值说明该错误是否表示一个文件或目录已经存在。ErrExist和一些系统调用错误会使它返回真。

```go
func check() bool {
	stat, err := os.Stat("/Users/anthony/Desktop/go-testt/go.mod")
	if err != nil {
		if os.IsExist(err) {
			return true
		}
		return false
	}
	return true
}
```

## Text/template

html模板 https://pkg.go.dev/text/template

# Gin

## Demo

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, Geektutu")
	})
	r.Run() // listen and serve on 0.0.0.0:8080
}
```

## 路由

基本使用

```go
// 设置路由
router := gin.Default()
// 第一个参数是：路径； 第二个参数是：具体操作 func(c *gin.Context)
router.GET("/Get", getting)
router.POST("/Post", posting)
router.PUT("/Put", putting)
router.DELETE("/Delete", deleting)
// 默认启动的是 8080端口
router.Run()

// 第一种方法:
r.GET("/", func(c *gin.Context) {
	c.String(http.StatusOK, "Who are you?")
})

// 第二种方法
r.POST("createApi", CreateApi)
func CreateApi(c *gin.Context) {
  // 代码
}
```

路由分组

```go
// 两个路由组，都可以访问，大括号是为了保证规范
v1 := r.Group("/v1")
{
    // 通过 localhost:8080/v1/hello访问，以此类推
    v1.GET("/hello", sayHello)
    v1.GET("/world", sayWorld)
}
v2 := r.Group("/v2")
{
    v2.GET("/hello", sayHello)
    v2.GET("/world", sayWorld)
}
r.Run(":8080")
```

大量路由实现

1. 建立`routers`包，将不同模块拆分到`多个go文件`
2. 每个文件提供`一个方法`，该方法注册实现所有的路由
3. 之后main方法在调用文件的方法实现注册



第一种实现方式

```go
// 这里是routers包下某一个router对外开放的方法
func LoadRouter(e *gin.Engine) {
    e.Group("v1")
    {
        v1.GET("/post", postHandler)
  		v1.GET("/get", getHandler)
    }
  	...
}

func main() {
    r := gin.Default()
    // 调用该方法实现注册
    routers.LoadRouter(r)
    routers.LoadRouterXXX(r) // 代表还有多个
    r.Run()
}
```

第二种方式

```go
像vue一样注册路由,没看懂项目  为啥要注册很多次
```

## 获取参数

获取body的数据,并绑定实体类

```go
func (b *BaseApi) Login(c *gin.Context) {
  // 声明实体类
	var l systemReq.Login
  // 赋值
	err := c.ShouldBindJSON(&l)
  // 获取ip
	key := c.ClientIP()
}


```

## 响应处理

```go
// 1.JSON
r.GET("/someJSON", func(c *gin.Context) {
    c.JSON(200, gin.H{
        "message": "Json",
        "status":  200,
    })
})
// 2.XML
r.GET("/someXML", func(c *gin.Context) {
    c.XML(200, gin.H{"message": "abc"})
})
// 3.YAML
r.GET("/someYAML", func(c *gin.Context) {
    c.YAML(200, gin.H{"name": "zhangsan"})
})
// 4.protobuf
r.GET("/someProtoBuf", func(c *gin.Context) {
    reps := []int64{1, 2}
    data := &protoexample.Test{
        Reps:  reps,
    }
    c.ProtoBuf(200, data)
})
```

## 中间件

中间件分为：**全局中间件** 和 **路由中间件**，区别在于前者会作用于所有路由。

```go
// 默认的中间件有
func BasicAuth(accounts Accounts) HandlerFunc // 身份认证
func BasicAuthForRealm(accounts Accounts, realm string) HandlerFunc
func Bind(val interface{}) HandlerFunc //拦截请求参数并进行绑定
func ErrorLogger() HandlerFunc       //错误日志处理
func ErrorLoggerT(typ ErrorType) HandlerFunc //自定义类型的错误日志处理
func Logger() HandlerFunc //日志记录
func LoggerWithConfig(conf LoggerConfig) HandlerFunc
func LoggerWithFormatter(f LogFormatter) HandlerFunc
func LoggerWithWriter(out io.Writer, notlogged ...string) HandlerFunc
func Recovery() HandlerFunc
func RecoveryWithWriter(out io.Writer) HandlerFunc
func WrapF(f http.HandlerFunc) HandlerFunc //将http.HandlerFunc包装成中间件
func WrapH(h http.Handler) HandlerFunc //将http.Handler包装成中间件
```

作用范围

```go
// 作用于全局
r.Use(gin.Logger())
r.Use(gin.Recovery())

// 作用于单个路由
r.GET("/benchmark", MyBenchLogger(), benchEndpoint)

// 作用于某个组
authorized := r.Group("/")
authorized.Use(AuthRequired())
{
	authorized.POST("/login", loginEndpoint)
	authorized.POST("/submit", submitEndpoint)
}
```

自定义中间件

```go
func Logger() gin.HandlerFunc {
	return func(c *gin.Context) {
		t := time.Now()
		// 给Context实例设置一个值
		c.Set("geektutu", "1111")
		// 请求前
		c.Next()
		// 请求后
		latency := time.Since(t)
		log.Print(latency)
	}
}
```

控制中间件

### 中间件控制的方法

gin提供了两个函数`Abort()`和`Next()`，二者区别在于：

> 1. next()函数会跳过当前中间件中next()后的逻辑，当**下一个中间件执行完成后**再执行剩余的逻辑
> 2. abort()函数执行终止当前中间件以后的中间件执行，**但是会执行当前中间件的后续逻辑**

举例子更好理解：

我们注册中间件顺序为`m1`、`m2`、`m3`，如果采用`next()`：

> 执行顺序就是
>
> 1. `m1的next()前面`、`m2的next()前面`、`m3的next()前面`、
> 2. `业务逻辑`
> 3. `m3的next()后续`、`m2的next()后续`、`m1的next()后续`。

那如果`m2`中间调用了`Abort()`，则`m3`和`业务逻辑`不会执行，只会执行`m2的next()后续`、`m1的next()后续`。

# Gorm

## CRUD 接口

## 创建

### 创建记录

```go
// 没有数据的字段,就是基本数据
user := User{Name: "Jinzhu", Age: 18, Birthday: time.Now()}

result := db.Create(&user) // 通过数据的指针来创建

user.ID             // 返回插入数据的主键
result.Error        // 返回 error
result.RowsAffected // 返回插入记录的条数
```

### 用指定的字段创建记录

创建记录并更新给出的字段。

```go
// 没有数据的字段,插入的是null
db.Select("Name", "Age", "CreatedAt").Create(&user)
// INSERT INTO `users` (`name`,`age`,`created_at`) VALUES ("jinzhu", 18, "2020-07-04 11:05:21.775")
```

创建一个记录且一同忽略传递给略去的字段值

```go
db.Omit("Name", "Age", "CreatedAt").Create(&user)
// INSERT INTO `users` (`birthday`,`updated_at`) VALUES ("2020-01-01 00:00:00.000", "2020-07-04 11:05:21.775")
```

<aside>
❓ 用指定的字段创建记录 不返回ID
</aside>

### 批量插入

```go
var users = []User{{Name: "jinzhu1"}, {Name: "jinzhu2"}, {Name: "jinzhu3"}}
db.Create(&users)

for _, user := range users {
  user.ID // 1,2,3
}

// 使用 CreateInBatches 分批创建时，你可以指定每批的数量
var users = []User{{name: "jinzhu_1"}, ...., {Name: "jinzhu_10000"}}
// 数量为 100
db.CreateInBatches(users, 100)
```

### 创建钩子

```go
type SysMenu struct {
	gorm.Model
	// 菜单名称
	Title string
	// 直属上级菜单标识
	ParentId int
	// 菜单URL
	Path string
	// 0：Flase,1：True
	AlwaysShow int
	// 组件
	Component string
	// 0：Flase,1：True
	Hidden int
	// 图标
	Icon string
	// 0：Flase,1：True
	NoCache string
	// 前端内部名字
	Name string
	// 前端内部路由
	Redirect string
	// 0：否，1：是
	IsButton int
	// 请求后端接口
	Ajax string
}

// 钩子
func (s *SysMenu) BeforeSave(tx *gorm.DB) (err error) {
	fmt.Println("BeforeSave")
	return
}

func (s *SysMenu) BeforeCreate(tx *gorm.DB) (err error) {
	fmt.Println("BeforeCreate")
	return
}

func (s *SysMenu) AfterCreate(tx *gorm.DB) (err error) {
	fmt.Println("AfterCreate")
	return
}

func (s *SysMenu) AfterSave(tx *gorm.DB) (err error) {
	fmt.Println("AfterSave")
	return
}
```

### 根据 Map 创建

```go
db.Model(&User{}).Create(map[string]interface{}{
  "Name": "jinzhu", "Age": 18,
})

// batch insert from `[]map[string]interface{}{}`
db.Model(&User{}).Create([]map[string]interface{}{
  {"Name": "jinzhu_1", "Age": 18},
  {"Name": "jinzhu_2", "Age": 20},
})
```

### 默认值

```go
type User struct {
  ID   int64
  Name string `gorm:"default:galeone"`
  Age  int64  `gorm:"default:18"`
}
```

# Cobra

## 简介

[cobra](http://github.com/spf13/cobra)是一个命令行程序库，可以用来编写命令行程序。

## 快速入门

安装包

```go
$ go get github.com/spf13/cobra/cobra
```

文件目录

```file
- my-go-test
    - cmd
        - version
        - root
    main.go
```


main.go

```go
import "go-test/cmd"

func main() {
	cmd.Execute()
}
```

version.go

```go
package cmd

import (
	"fmt"

	"github.com/spf13/cobra"
)

var versionCmd = &cobra.Command{
	Use:   "version",
	Short: "显示计算器版本",
	Long:  "显示计算器版本",
	Run: func(cmd *cobra.Command, args []string) {
		fmt.Println("anthony's jsq version 0.0.1")
	},
}

func init() {
	rootCmd.AddCommand(versionCmd)
}
```

root.go

```go
import (
	"fmt"
	"github.com/spf13/cobra"
)

var rootCmd = &cobra.Command{
	Use:   "jsq",
	Short: "计算器",
	Long:  `anthony写的计算器`,
	Run: func(cmd *cobra.Command, args []string) {
		fmt.Println("anthony写的计算器,请使用 ./jsq -h")
	},
}

func Execute() {
	rootCmd.Execute()
}
```

## 打包运行

```bash
# 打包
➜  go-testt go build -o jsq

# 运行不带参数的命令
➜  go-testt ./jsq 
anthony写的计算器,请使用 ./jsq -h

# 运行主命令的help
➜  go-testt ./jsq -help
Error: unknown shorthand flag: 'e' in -elp
Usage:
  jsq [flags]
  jsq [command]

Available Commands:
  completion  Generate the autocompletion script for the specified shell
  help        Help about any command
  version     显示计算器版本

Flags:
  -h, --help   help for jsq

Use "jsq [command] --help" for more information about a command.

# 运行子命令
➜  go-testt ./jsq version -h
显示计算器版本

Usage:
  jsq version [flags]

Flags:
  -h, --help   help for version
```

包成window包

```bash
go build -o jsq.exe
```

# 库

## Yaml解析库

```shell
go get gopkg.in/yaml.v3
```

测试文件yaml

```yaml
mysql:
  url: 127.0.0.1
  port: 3306

redis:
  host: 127.0.0.1
  port: 6379
```
将 `yaml` 文件的数据转成自定义的结构体或 `Map`
```go
import (
	"fmt"
	"gopkg.in/yaml.v3"
	"os"
)

type Config struct {
	Mysql Mysql `json:"mysql"`
	Redis Redis `json:"redis"`
}

type Mysql struct {
	Url  string
	Port int
}

type Redis struct {
	Host string
	Port int
}

func main() {
	dataBytes, err := os.ReadFile("test.yaml")
	if err != nil {
		fmt.Println("读取文件失败：", err)
		return
	}
	fmt.Println("yaml 文件的内容: \n", string(dataBytes))
	config := Config{}
	err = yaml.Unmarshal(dataBytes, &config)
	if err != nil {
		fmt.Println("解析 yaml 文件失败：", err)
		return
	}
	fmt.Printf("config → %+v\n", config) // config → {Mysql:{Url:127.0.0.1 Port:3306} Redis:{Host:127.0.0.1 Port:6379}}

	mp := make(map[string]any, 2)
	err = yaml.Unmarshal(dataBytes, mp)
	if err != nil {
		fmt.Println("解析 yaml 文件失败：", err)
		return
	}
	fmt.Printf("map → %+v", config) // config → {Mysql:{Url:127.0.0.1 Port:3306} Redis:{Host:127.0.0.1 Port:6379}}

}


```

## yaml解析库

```shell
go get github.com/spf13/viper
```

测试

```go

import (
    "fmt"
    "github.com/spf13/viper"
)

func main() {
    // 设置配置文件的名字
    viper.SetConfigName("test")
    // 设置配置文件的类型
    viper.SetConfigType("yaml")
    // 添加配置文件的路径，指定 config 目录下寻找
    viper.AddConfigPath("./config")
    // 寻找配置文件并读取
    err := viper.ReadInConfig()
    if err != nil {
            panic(fmt.Errorf("fatal error config file: %w", err))
    }
    fmt.Println(viper.Get("mysql"))     // map[port:3306 url:127.0.0.1]
    fmt.Println(viper.Get("mysql.url")) // 127.0.0.1
}
```

# 笔记

## 字符串互转byte

```go
// string to []byte
s1 := "hello" b := []byte(s1)
// []byte to string
s2 := string(b)
```

创建对象的方式

```go
type Car struct {
    color string
    size  string
}

// 方式一：使用T{…}方式，结果为值类型
c := Car{}

// 方式二：使用new的方式，结果为指针类型
c1 := new(Car)

// 方式三：使用&方式，结果为指针类型
c2 := &Car{}

// 以下为创建并初始化
c3 := &Car{"红色", "1.2L"}
c4 := &Car{color: "红色"}
c5 := Car{color: "红色"}

// 构造函数：
// 在Go语言中没有构造函数的概念，对象的创建通常交由一个全局的创建函数来完成，以
// NewXXX 来命名，表示“构造函数” ：
func NewCar(color,size string)*Car  {
    return &Car{color,size}
}
```

# 报错

1.debug call has arguments but no formatting directives

打印/日志 格式解析错误,错误的原因就是没加`%v`

2.取消代理

```shell
go env
GOPROXY='https://goproxy.cn,direct'  # 这个是国内的代理,在国外不容易下载依赖

➜  server git:(main) ✗ go env -u GOPROXY  # 取消代理,使用默认的配置
➜  server git:(main) ✗ go env
GOPROXY='https://proxy.golang.org,direct'
```

