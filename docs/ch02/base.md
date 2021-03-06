# Go基础

这小节我们将要介绍如何定义变量、常量、Go内置类型以及Go程序设计中的一些技巧。   
Go语言中语句结束不需要使用(可以使用也可以不使用，默认gofmt会去掉)分号`;`来作为结束符。

Go语言内置：
```
类型:
	bool byte complex64 complex128 error float32 float64
	int int8 int16 int32 int64 rune string
	uint uint8 uint16 uint32 uint64 uintptr

常量:
	true false iota

空值:
	nil

函数:
	append cap close complex copy delete imag len
	make new panic print println real recover
```


## Go程序基本规则
1. Go语言命名规则：
  - 大写字母开头的变量是可导出的，也就是其它包可以读取的，是公用变量；小写字母开头的就是不可导出的，是私有变量。
  - 大写字母开头的函数也是一样，相当于JAVA中`class`里的带`public`关键词的公有函数；
    小写字母开头的就相当于有`private`关键词的私有函数。

2. Go语言包规则
当使用`import`关键词导入某个包后，就可以通过包名来获取该包下所有公开属性或函数。   
还记得前面曾经设置过**GOPATH**这个路径变量，导入包到项目时候，是以`$GOPATH/src`为根导入。如：
```
// 注意，这里如果GOPATH设置多个路径时会出错
$ tree -L 2 $GOPATH/src
|-- github.com
      |-- astaxie
          |-- beego
|-- hello
      |-- world
```
这样，我们在导入`beego`包的时候，就需要这样写`import "github.com/astaxie/beego"`。   
当我们需要导入`world`包的时候，就应该这样写`import "hello/world"`。
在Go 1.6版本及后续版本中，可以在项目下新建`vendor`文件夹，将需要用到的库和依赖放入其中。
引用的时候，直接引用包名即可。

详细导包规则参见：[import](./function.md#import)

## 注释
在Go语言中，提供两种格式的注释：`/* */` 与 `//`。这两种格式注释与大多数C系语言相同，分别为块注释和行注释。

示例:
```go
// 这是行注释，只能注释单行
var a = "abc"

/*
 这里是块注释，可以注释单行或多行
*/
func main() {
}

```

## 变量

Go语言里面定义变量有多种方式。

使用`var`关键字是Go最基本的定义变量方式，Go把变量类型放在变量名后面：

```go
//定义一个名称为“variableName”，类型为"type"的变量
var variableName type

// 如下例所示
var name string
```

定义多个变量
```go
//定义三个类型都是“type”的三个变量
var vname1, vname2, vname3 type
// 如下例所示
var a, b, c int
```

定义变量并初始化值
```go
//初始化“variableName”的变量为“value”值，类型是“type”
var variableName type = value
// 如下例所示
var name string = "golang"
```

同时初始化多个变量
```go
/*
	定义三个类型都是"type"的三个变量,并且它们分别初始化相应的值
	vname1为v1，vname2为v2，vname3为v3
*/
var vname1, vname2, vname3 type = v1, v2, v3
// 如下例所示
var a, b, c int = 1, 2, 3
```

我们也可以直接忽略类型声明，那么上面的代码变成这样了：
```go
/*
	定义三个变量，它们分别初始化相应的值
	vname1为v1，vname2为v2，vname3为v3
	然后Go会根据其相应值的类型来帮你初始化它们
*/
var vname1, vname2, vname3 = v1, v2, v3
// 如下例所示, a为int类型、b为string类型、c为bool类型
var a, b, c = 12, "go", false
```

如果你觉得上面的还是有些繁琐，还可以继续简化：
```go
/*
	定义三个变量，它们分别初始化相应的值
	vname1为v1，vname2为v2，vname3为v3
	编译器会根据初始化的值自动推导出相应的类型
*/
vname1, vname2, vname3 := v1, v2, v3
// 如下例所示
a, b, c := 123, "golang", true
```

现在是不是看上去非常简洁了？`:=`这个符号直接取代了`var`和`type`,这种形式叫做简短声明。   
不过它有一个限制，那就是它只能用在函数内部；在函数外部使用则会无法编译通过，所以一般用`var`方式来定义全局变量。

#### 小结
综上可知，Go语言定义变量赋值有三种方式：
```go
// 第一种, var + 变量名 + 变量类型
var a int = 3

// 第二种, var + 变量名
var b = 3.1415926

// 第三种, 变量名 := 值
c := "Hello"
```

示例:
```go
package main
import "fmt"

// 第一种用法
var a int = 4
// 第二种用法
var b = 1.23

func main() {
	// 这里第三种用法，只能用在函数体内
	c := "Hello"

	fmt.Println(a);
	fmt.Println(b);
	fmt.Println(c);
}
```

**多重赋值**   
由上面可以看到，在Go语言中可以多重赋值，即支持`a, b = 2, 3`这种形式，而不需要`a = 2, b = 3`这样。   
比如用这个特性来写冒泡算法，就可以直接使用`i, j = j, i`这样的写法，而不用多新建一个变量了。

------

`_`（下划线）    
是个特殊的变量名，任何赋予它的值都会被丢弃。   
在下面这个例子中，我们将值`35`赋予`b`，并同时丢弃`34`：
```go
_, b := 34, 35
```
还可以在导入包的时候这样使用：
```go
import (
  "fmt"
  _ "github.com/test/test"
)
```
这样的话，会忽略`test`包中所有内容，但是会执行`test`包中的`init()`函数。   
这个特性在某些时候会非常有用，特别是需要某些初始化后才能进行操作的情况。

**Go对于已声明但未使用的变量会在编译阶段报错**，比如下面的代码就会产生一个错误：声明了`i`但未使用。
```go
package main

func main() {
	var i int
}
```

#### 变量初始化
在Go语言中，变量在定义而没有初始化的情况下，会自动进行初始化。  
如下面所示：
```go
var a int           // a的值为: 0      类型为: int
var b string        // b的值为: ""     类型为: string
var c interface{}   // c的值为: nil    类型为: interface{}
var d sync.Pool     // d的值为: Pool{} 类型为: sync.Pool
var e *sync.Pool    // e的值为: nil    类型为: *sync.Pool
```
另外，Go函数的命名返回值也会自动进行初始化，如下：
```go
func hello(name string) (age int, err error) {
  // age的值为0, 类型为int
  // err的值为nil, 类型为error
}

// !!!请注意此处
func do(body string) (p *People) {
  // 此处p的值为nil,未进行内存分配
  json.Unmarshal([]byte(body), p)
  // 像上面这样直接对p进行操作，会引会内存错误，必须显式初始化
  // 如：p = new(People)
}
```


## 常量

所谓常量，也就是在程序编译阶段就确定下来的值，而程序在运行时则无法改变该值。   
在Go程序中，常量可定义为数值、布尔值或字符串等类型。

它的语法如下：
```go
const constantName = value
//如果需要，也可以明确指定常量的类型：
const Pi float32 = 3.1415926
```

下面是一些常量声明的例子：
```go
const Pi = 3.1415926
const i = 10000
const MaxThread = 10
const prefix = "foo_"
```

Go 常量和一般程序语言不同的是，可以指定相当多的小数位数(例如200位)，
若指定給float32自动缩短为32bit，指定给float64自动缩短为64bit。
详情参考[链接](http://golang.org/ref/spec#Constants)

##### Tips
常量的右值也可以是编译期能运行得出的值，如`const m = 1 << 3`。


## 内置基础类型

### Boolean--布尔类型

在Go中，布尔值的类型为`bool`，值是`true`或`false`，默认为`false`。
```go
//示例代码
var isActive bool  // 变量声明
var enabled, disabled = true, false  // 忽略类型的声明
func test() {
	var available bool  // 一般声明
	valid := false      // 简短声明
	available = true    // 赋值操作
}
```


### 数值类型

**整数** 类型有无符号和带符号两种。
Go同时支持`int`和`uint`，这两种类型的长度(占用字节数)相同，但具体长度取决于不同编译器的实现。
Go里面也有直接定义了长度的类型：   
`rune`, `int8`, `int16`, `int32`, `int64`和`byte`, `uint8`, `uint16`, `uint32`, `uint64`。   
其中`rune`是`int32`的别称，`byte`是`uint8`的别称。

>需要注意的一点是，这些类型的变量之间不允许互相赋值或操作，不然会在编译时引起编译器报错。
>
>如下的代码会产生错误：invalid operation: a + b (mismatched types int8 and int32)
>
>```go
> var a int8
> var b int32
> c := a + b
>```
>另外，尽管32位平台上int的长度是32 bit, 但int与int32并不可以互用。

**浮点数** 类型有`float32`和`float64`两种（没有`float`类型），默认是`float64`。   
因为浮点数是一种不精确的表达方式，所以直接用`==`来进行两个浮点数的比较可能会出现意外的结果。   

Go还支持 **复数** 类型。它的默认类型是`complex128`（64位实数+64位虚数）。
如果需要小一些的，也有`complex64`(32位实数+32位虚数)。
复数的形式为`RE + IMi`，其中`RE`是实数部分，`IM`是虚数部分，而最后的`i`是虚数单位。
下面是一个使用复数的例子：
```go
var c complex64 = 5+5i
//输出: (5+5i)
fmt.Printf("Value is: %v", c)
```

### 字符串
我们在上一节中讲过，Go中的字符串都是采用`UTF-8`字符集编码。
字符串是用一对双引号（`""`）或反引号（`` ` `` `` ` ``）括起来定义，它的类型是`string`。
```go
//示例代码
var frenchHello string  // 声明变量为字符串的一般方法
var emptyString string = ""  // 声明了一个字符串变量，初始化为空字符串
func test() {
	no, yes, maybe := "no", "yes", "maybe"  // 简短声明，同时声明多个变量
	japaneseHello := "Konichiwa"  // 同上
	frenchHello = "Bonjour"  // 常规赋值
}
```

在Go中字符串是不可变的，例如下面的代码编译时会报错：cannot assign to s[0]
```go
var s string = "hello"
s[0] = 'c'
```

但如果真的想要修改怎么办呢？下面的代码可以实现：
```go
s := "hello"
c := []byte(s)  // 将字符串 s 转换为 []byte 类型
c[0] = 'c'
s2 := string(c)  // 再转换回 string 类型
fmt.Printf("%s\n", s2)
```

Go中可以使用`+`操作符来连接两个字符串：
```go
s := "hello,"
m := " world"
a := s + m
fmt.Printf("%s\n", a)
```

修改字符串也可写为：
```go
s := "hello"
s = "c" + s[1:] // 字符串虽不能更改，但可进行切片操作，关于切片稍后会讲到
fmt.Printf("%s\n", s)
```

如果要声明一个多行的字符串怎么办？可以通过`` ` ``来声明：
```go
m := `hello
	world`
```

`` ` `` 括起的字符串为Raw字符串，即字符串在代码中的形式就是打印时的形式，它没有字符转义，换行也将原样输出。
例如本例中会输出：
```
hello
	world
```

##### 获取字符串长度
由于Go语言使用UTF8编码的，所以，直接使用`len`函数来获取字符串长度，在某些情况下返回的结果可能不是你想要的。  
例如：  
```go
var str = "Hello世界"
fmt.Println(len(str))    // 11
```
可以看到，打印出来的长度11很可能不是你想要的，你可能想要的是**7**。在这种情况下，你就需要使用`"unicode/utf8"`这个包中的函数来计算。如：
```go
var str = "Hello世界"
fmt.Println(utf8.RuneCountInString(str))   // 7
```




### 错误处理
Go内置有一个`error`接口，专门用来处理错误信息，Go的`package`里面还专门有一个包`errors`来处理错误：
```go
err := errors.New("emit macho dwarf: elf header corrupted")
if err != nil {
	fmt.Print(err)
}
```

其中，`error`接口的定义如下:
```go
type error interface {
	Error() string
}
```
对于大多函数，如果要返回错误，可以将错误信息作为多个返回值的最后一个:
```go
func Foo(param int) (ret int, err error) {
	// ...
}
```

### Go数据底层的存储

下面这张图来源于[Russ Cox Blog](http://research.swtch.com/)中一篇介绍
[Go数据结构](http://research.swtch.com/godata)的文章，
大家可以看到这些基础类型底层都是分配了一块内存，然后存储了相应的值。

![](../images/2.2.basic.png?raw=true)

图2.1 Go数据格式的存储

## 一些技巧

### 分组声明
在Go语言中，同时声明多个常量、变量，或者导入多个包时，可采用分组的方式进行声明。

例如下面的代码：
```go
import "fmt"
import "os"

const i = 100
const pi = 3.1415
const prefix = "Go_"

var i int
var pi float32
var prefix string
```

可以分组写成如下形式：
```go
import (
	"fmt"
	"os"
)

const (
	i = 100
	pi = 3.1415
	prefix = "Go_"
)

var (
	i int
	pi float32
	prefix string
)
```

> 除非被显式设置为其它值或`iota`，每个`const`分组的第一个常量被默认设置为它的0值，
> 第二及后续的常量被默认设置为它前面那个常量的值，如果前面那个常量的值是`iota`，则它也被设置为`iota`。

### iota枚举
Go里面有一个关键字`iota`，这个关键字用来声明`enum`的时候采用，它默认开始值是0，每调用一次加1：
```go
const (
	x = iota  // x == 0
	y = iota  // y == 1
	z = iota  // z == 2
	w  // 常量声明省略值时，默认和之前一个值的字面相同。这里隐式地说w = iota，因此w == 3。
)

const v = iota // 每遇到一个const关键字，iota就会重置，此时v == 0

const (
  e, f, g = iota, iota, iota //e=0,f=0,g=0 iota在同一行值相同
)
```

## 运算符
Go语言中运算符与C系语言中运算符大致相似。
