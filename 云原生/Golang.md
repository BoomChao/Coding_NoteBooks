### 一、基本语法

Golang 设计理念：一种事情有且只有一种方法完成

#### 1.软件包安装

直接官网下载好，配置安装下环境变量即可

```bash
/etc/profile	# 在这个文件写入会对所有用户生效
~/.bashrc		# 在这个文件写入只会对当前用户生效

# 以上两个文件中任意选择一个
# 将Go的安装目录记录下，比如安装目录为 /home/CHAO/Document/Go/bin
export GOROOT=/home/CHAO/Document/Go	#这个表示go的安装目录
export PATH=$PATH:$GOROOT/bin			#声明环境变量
export GOPATH=/home/CHAO/Golang			#这个表示go的工作目录
```



#### 2.基础语法

声明变量的三种方式

```Go
var foo int 		//方式一
foo = 10

var foo int = 20	//方式二

foo := 30			//方式三
```



Go 语言禁止对常量取地址的操作；编译器不允许在类型变量之间进行隐式类型转换



##### 浮点类型

Go 的浮点类型有固定的范围和字段长度，不受OS的影响

Go 的浮点型默认声明为 float64 类型，通常情况下应该使用 float64，因为它比 float32 位更加精确



##### 字符类型

Go 中没有**专门的字符类型**，如果要存储单个字符（字母），一般使用 byte 来保存；Go 的字符串是由字符组成的
Go 语言中的字符使用 UTF-8 编码 (其中英文字母占1个字节，汉字占用3个字节)

字符串就是由一串固定长度的字符连接起来的字符序列；Go 的字符串是由单个字节连接起来的，也就是说对于传统的字符串是由字符组成的，而 Go 中字符串却是由字节组成的

Golang 和 Java/C 不同，Go 在不同类型变量之间**赋值时需要显示转换，也就是说 Go 中的数据类型不能自动转换**

Go 中的字符串是不可变的，一旦初始化后便不能再重新赋值

字符串的两种表示形式：

1.双引号（ ” “ ）：会识别出转义字符

2.反引号（\` `）：以字符串的原生形式输出，包括换行和特殊字符，可以实现防止攻击，输出源代码等效果



字符串和数值型互相转化

数值转化成字符型利用 fmt 包和 strconv 包

```go
var num1 int = 99
var num2 float64 = 23.32
var b bool = true

//1.方法一：使用 Sprintf
str1, str2, str3 := fmt.Sprintf("%d", num1), fmt.Sprintf("%f", num2), fmt.Sprintf("%t", b)

//2.方法二：使用 FormatInt
str1 := strconv.FormatInt(int64(num1), 10)
str2 := strconv.FormatFloat(num2, 'f', 10, 64)	// 'f'表示格式，10表示小数位保留10位，64代表这个小数是float64
str3 := strconv.FormatBool(b)
```

string 类型转换成基本数据类型

```go
var str string  = "true"
var b bool
b, _ = strconv.ParseBool(str)

var str2 string = "123456"
var n1 int64
n1, _ = strconv.ParseInt(str2, 10, 64)	//10代表十进制，64代表转换成int64
```



常见的值类型和引用类型

值类型：基本数据类型 int系列、float系列、bool、string、数组以及结构体等，通常存放在**栈区**

引用类型：指针、slice切片、map、管道chain、interface等，通常存放在**堆区**



##### 算法运算符

a/b = a - a/b*b

++ 和 -- 只能独立使用，Go中只有后置++和--，没有前置++

```Go
var i int = 3
var n int = i++		//这是错误的
i++
var n int =  i		//这是正确的
```



##### 分支循环

Go 支持在 if 中，直接定义一个变量，比如下面

```Go
if age := 18; age > 20 {
    fmt.Println("OK")
} else {
    fmt.Println("NO")
}
```



##### 函数和包

```go
基本语法：
func 函数名(形参列表) (返回值列表) {
    执行语句...
    return 返回值列表
}
```



Go 中的包：**包的实质就是创建不同的文件夹，来存放程序文件**

包的三大作用：1.区分相同名字的函数，变量和标识符		2.当程序文件很多时，可以很好的管理项目		3.控制函数，变量等访问范围，即作用域

打包基本语法：package 包名		引入一个包：import  "包的路径"



Go 支持可变参数

```Go
func sum(args ... int) int {
    ...
}
```

如果一个函数的形参列表中有可变参数，则可变参数需要放在形参列表后面



每一个源文件都可以包含一个 init 函数，该函数会在 main 函数之前执行，被 Go 运行框架调用，也就是说 init 会在 main 函数前被调用

通常可以在 init 函数中完成初始化工作

闭包就是一个函数和与其相关的引用环境组合的一个整体（实体）



##### defer语句

在函数中，程序员经常需要创建资源（比如：数据库了解、文件句柄、锁等），为了在函数执行完毕后，及时的释放资源，Go 的设计者提供了defer（延时机制）



##### 参数类型

值类型：基本数据类型 int 系列，float系列，bool，string，数组和结构体 struct

引用类型：指针、slice切片、map、管道chan、interface 等都是引用类型

Golang 在函数体外不能有赋值语句

```Go
var age int = 20	//正确
name := "Tom"		//错误，这一句实际上有两句，var name string;  name = "Tom";  第二句为赋值语句
```



##### 字符串

```go
len(str)							//1.统计字符串的长度(字节数)

n, err := strconv.Atoi("12")		//2.字符串转数字

str := strconv.Itoa(12345)			//3.数字转字符串

var bytes = []byte("hello go")		//4.字符串转byte

str := string([]byte{97,98,99})		//5.[]byte转字符串

str := strconv.FormatInt(123, 2)	//6.十进制转化成对应的二进制，八进制，十六进制，返回的是一个字符串

b := strings.Contains("seafood", "foo")		//7.查找子串是否在指定的字符串中

num := strings.Count("chesses", "e")	//8.判断字符串有几个指定的子串

b := strings.EqualFold("abc", "ABC")	//9.不区分大小写的字符串比较

n := strings.Index("NIL_abc", "abc")	//10.返回子串在字符串中出现的第一次位置，找不到返回-1

index := strings.LastIndex("go golang", "go")	//11.返回子串在字符串出现的最后一次位置

str := strings.Replace("go go hello", "go", "C++", -1)  //12.字符串替换，-1表示全部替换

strings.Split("hello,world,ok", ",") 	//13.字符串拆分

strings.ToLower("Go"), ToUpper("Go")	//14.字符串大小写转换

strings.TrimSpace(" hi, world  ")		//15.去除字符串两边的空格

strings.Trim("! hello ! ", "! ")	//16.去除左右两边指定的字符串

strings.TrimLeft("! hello!", "!")	//17.去掉左边的指定字符串

strings.HasPrefix("ftp://192.168...", "ftp")   HasSuffix("Hi.jpg", "jpg")	//18.判断是否以指定的字符串开头或者结尾
```



##### 错误处理

Go 追求简洁，所以不支持 try...catch...finally 这种处理

Go 中引入的处理方式是：defer  panic  recover

这几个异常的使用场景可以这么简单描述：Go 中可以抛出一个 panic 异常，然后在 defer 语句中通过 recover 捕获这个异常，然后正常处理



#### 3.数组与切片

初始化数组的几种方式

```go
var intArr [3]int = [3]int{10, 20, 30}		//1.方法一

var intArr = [3]int{10, 20, 30}	//2.方法二

var intArr = [...]int{10, 20, 30}	//3.方法三

var intArr = [...]int{1:80, 0:90, 2:99}		//4.方法四

// 前面的 var intArr 全部可以替换成 intArr := 
```



切片是数组的一个引用，因此切片是引用类型，在进行传递时，遵守引用的机制

切片的长度是可以变化的，因此切片是一个可以动态变化的数组

slice 是一个引用类型，从底层来说是一个数据结构（struct 结构体）

```go
type slice struct {
    array unsafe.Pointer // 被引用的数组中的起始元素地址，之前版本是 ptr *[]T
    len int
    cap int
}
```



slice 的使用，slice也可以称为动态数组  源码在 **($GOROOT/src/runtime/slice.go)**

1.使用数组创建一个切片

2.使用 make 来创建切片

3.定义切片并直接指定数组

```go
//1.使用数组初始化
var intArr [5]int = [...]int{10, 20, 30, 40, 50}
slice := intArr[1:3]

//2.使用 make 创建切片，但是make底层也会创建一个数组，由切片在底层创建一个数组
var slice []int = make([]int, 4, 10)	//4表示切片大小，10表示容量

//3.定义切片直接指定数组
var strSlice []string = []string{"Tom", "Jack", "Mary"}		//注意与数组的区别，数组后面的"[]"是要有数值的，而切片的"[]"要求是空的

//4.对切片添加元素
slice = append(slice, 99)
//切片的扩容机制，append的时候，如果长度增加后超过容量，则将容量增加两倍
```

切片定义后，还不能使用，因为本身是一个空的，需要让其引用到一个数组，或者 make 一个空间供切片使用

slice 可以看成一个 struct 结构体，其中有数据成员存储指向的元素，存储数组大小以及容量



切片操作并不复制切片指向的元素，创建一个新的切片会复用原来切片的底层数组，因此切片操作是非常高效的。下面的例子展示了这个过程

<img src="https://raw.githubusercontent.com/BoomChao/Figure/main/slice.jpg" alt="golang slice" style="zoom: 67%;" />  

最终 nums 为 [1,2,3,4,50]



string 底层是一个 byte 数组，因此 string 也可以进行切片处理；string 是不可变的，也就是不能通过 str[0] = 'z' 方式来修改字符串 

如果需要修改字符串，需要先将 string -> []byte 或者 []rune，修改后再重新转化成字符串

byte 就是 uint8 类型，rune 就是 uint32 类型

```go
//string 底层是一个byte数组，因此string也可以进行切片处理
str := "hello,world"
slice := str[6:]

//对字符串进行间接修改
str := "hello"
arr := []byte(str)		//如果字符含有中文则转化成byte会溢出，此时转化成rune即可, arr := []rune(str)
arr[0] = 'W'
str = string(arr)		//将byte[]转化成string
```



**例1：基于数组创建切片**

```go
arr := [...]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
fmt.Println(arr[:3:5][:4]) // [1 2 3 4]
fmt.Println(arr[:3:5][:8]) // panic: runtime error: slice bounds out of rang
```

`arr[:3:5]` 基于 `arr` 创建一个 slice, len 是 3, cap 是 5; 然后再在这个 slice 的基础上分别创建一个 `len = 4` 和 `len = 8` 的 slice. 

前者运行正常, 后者因超出 `cap = 5` 范围而 panic, 尽管后者实际想要的内存并没有超出 arr 数组范围



**例2：go语言中切片当作函数传递**

```go
func Change(s []int){
    s[0]=11
    s[1]=22
}
func main(){
    slice:=[]int{1,2,3,4,5}
    Change(slice)
    fmt.Println(slice)		//打印结果为 {11,22,3,4,5}
}
----
func Add(s []int){
    s = append(s,6,7,8)
}
func main(){
    slice:=[]int{1,2,3,4,5}
    Add(slice)
    fmt.Println(slice)		//结果不变，照样为 {1,2,3,4,5}
}
```

因为 append 扩容使得地址发生了变化，所以不是指向原来的切片也就导致了并不是在原来的切片上面增加了元素

Add函数里面**用了append( )函数**，在调用函数时，在栈区里面把`1 2 3` 添加到 a 里面然后重新分配了地址，而原来的 s 切片还是指向原来地址，根本没有变，所以在 main 函数里面打印出 s 还是原来的，不会改变

解决方案如下，将修改后的地址传递回去即可

```go
func Add(s []int) (b []int) {
    b = append(s, 6,7,8)
    return 
}

func main() {
    slice := []int{1,2,3,4,5}
    slice = Add(slice)
    fmt.Println(slice)		//结果为 {1,2,3,4,5,6,7,8}
}
```



**例3：两道 Leetcode 算法题**

Leetcode第131题 (关于切片slice的大坑--一定要注意)

```go
func partition(s string) [][]string {

	res := [][]string{}

	n := len(s)
	dp := make([][]int, n)
	for i := 0; i < n; i++ {
		dp[i] = make([]int, n)
	}

	for i := n - 1; i >= 0; i-- {
		for j := i; j < n; j++ {
			if s[i] == s[j] && (j-i <= 2 || dp[i+1][j-1] == 1) {
				dp[i][j] = 1
			}
		}
	}
    
	path := []string{}

	dfs(s, 0, dp, path, &res)

	return res
}

func dfs(s string, pos int, dp [][]int, path []string, res *[][]string) {
	if pos == len(s) {
		//fmt.Println(path)
		*res = append(*res, path)		//1.这种写法会有bug
        *res = append(*res, append([]string{}, path...))	//2.这种写法没有bug
		//fmt.Println(*res)
		return
	}

	for i := pos; i < len(s); i++ {
		if dp[pos][i] == 1 {
			path = append(path, s[pos:i+1])
			dfs(s, i+1, dp, path, res)
			path = path[:len(path)-1]
		}
	}

}
```

Leetcode第382题：蓄水池抽样问题(着重看这代码的写法)

```go
type ListNode struct {
	Val  int
	Next *ListNode
}

type Solution struct {
	head *ListNode
}

func Constructor(head *ListNode) Solution {
	return Solution{
		head: head,
	}
}

func (this *Solution) GetRandom() int {
	n, res := 2, this.head.Val
	cur := this.head.Next

	for cur != nil {
		if rand.Intn(n) == 0 {
			res = cur.Val
		}
		n++
		cur = cur.Next
	}

	return res
}
```



**例4：对二维数组排序**

```go
//将二维数据按照首元素重排
nums := [][]int{{2, 3}, {1, 4}, {8, 9}, {7, 10}}

//方法一
sort.Slice(nums, func(i, j int) bool {
    return nums[i][0] < nums[j][0]
})

//方法二：实现sort.Interface接口，只要实现了这个接口，就可以调用sort包中的函数进行排序
sort.Sort(ints(nums))

type ints [][]int

func (s ints) Len() int           { return len(s) }
func (s ints) Less(i, j int) bool { return s[i][0] < s[j][0] }
func (s ints) Swap(i, j int)      { s[i], s[j] = s[j], s[i] }
```





#### 4.数据结构和算法

区分是否内外排序的依据就是判断数据是否全部加载进内存中，如果全部加载进内存中就是那就是内排序

map 在使用之前一定要 make , map 的 key 是不能重复的，如果重复了，就以最后这个 key-value 为准

map 的 key-value 是无序的，**在使用 mp 前，一定先用 make 给 map 分配数据空间**

map 的三种声明方式

```go
//1.第一种方式
var mp map[string]string
mp = make(map[string]string, 10)

//2.第二种方式
mp := make(map[string]string)		//后面这个容量可以不预先指定

//3.第三种方式
mp := map[string]string {
    "one" : "CPP",
    "two" : "Java",
    "three": "Python",
}

//删除 map 中的某个元素
delete(mp, "1")
```

Go 中没有一个专门的方法针对 map 中的 key 进行排序

**Go 中的 map 默认是无序的，注意也不是按照添加的顺序存放的，你每次遍历可能得到的结果都不一样**

Go 中的 map  排序先是将 key 进行排序，之后根据 key 值遍历输出即可



golang 对 map 排序输出

```go
// 1.先将mp的key放入到切片中
// 2.对切片排序
// 3.遍历切片，然后按照key来输出map的值
mp := make(map[int]int, 10)
mp[1] = 100
mp[3] = 23
mp[2] = 383

var keys []int
for k, _ := range mp {
    keys = append(keys, k)
}

sort.Ints(keys)
fmt.Println(keys)

for _, k := range keys {
    fmt.Printf("map[%v] = %v\n", k, mp[k])
}
```



Go 不支持泛型，比如 C++ 里面的 max 和 min 方法，Go 中只有比较 float64 的 Min 和 Max 方法

```go
math.Min(float64, float64) float64
math.Max(float64, float64) float64
```

另外为了保持 Go 简洁干净，Go 不支持泛型，所以既然有了对比 float64 数据类型的 max/min 方法，在同一个 math 包中就无法支持同名的但是参数不同的方法，也就是下面的方法无法存在同一个包中

```go
math.Max(float64, float64) float64
math.Max(int64, int64) int64
```





#### 5.面向对象编程

1.Go 也支持面向对象编程 (OOP)，但是和传统的面向对象编程还是有区别，并不是纯粹的面向对象语言；所以我们说 Golang 支持面向对象编程特性是比较准确的

2.Golang 没有类 (class)，Go 语言的结构体 (struct) 和其他编程语言的类有同等地位

3.Golang 面向对象编程非常简洁，去掉了传统 OOP 语言的继承、方法重载、构造函数和析构函数、隐藏的 this 指针等

4.Golang 仍然有面向对象编程的继承，封装和多态特性，只是实现的方式和其他OOP不一样，比如继承是通过匿名字段来实现的

5.Golang 面向编程很优雅，OOP 本身就是语言类型系统 (type system) 的一部分，通过 interface 接口，耦合性低，也非常灵活



如何声明结构体

```go
type 结构体名称 struct {
    field1 type
    field2 type
}
```

**使用 slice 和 map 一定要先 make 才能使用**



结构体是用户单独定义的类型，和其他类型进行转换时需要有完全相同的字段 (名字，个数和类型)

结构体进行 type 重新定义 (相当于取别名)，golang 认为是新的数据类型，但是相互间可以强转

struct 的每个字段上，可以写上一个 tag，这个 tag 可以通过反射机制获取，常见的使用场景就是序列化和反序列化



golang 多重继承

如果一个 struct 嵌套了多个匿名结构体，那么该结构体可以直接访问嵌套的匿名结构体的字段和方法，从而实现了多重继承

但是为了保证代码的间接性，建议大家尽量不要使用多重继承



接口（interface），其本质是一个指针

interface 类型可以定义一组方法，但是这些不需要实战；并且 interface 不能包含任何变量，到某个自定义类型（比如 struct）要使用的时候，再根据具体情况把这些方法写出来

1.接口里面的方法都没有方法体，即接口的方法都是没有实现的方法；接口体现了程序设计的多态和高内聚低耦合的思想

2.Golang 的接口，不需要显示的定义；只要一个变量，含有接口类型的所有方法，那么这个变量就实现这个接口，因此 golang 中没有 implement 这样的关键字

3.在 Go 中，一个自定义类型需要将某个接口的所有方法都实现，我们说这个自定义类型实现了这个接口

4.一个自定义类型只有实现了某个接口，才能将该自定义类型的实例（变量）赋值给接口类型

5.只要是自定义数据类型，就可以实现接口，不仅仅是结构体类型

6.一个自定义类型可有又多个接口

7.Golang 接口中不能有任何变量

8.一个接口（比如 A 接口），可以继承多个别的接口（比如 B，C 接口），这是如果要实现A接口，也必须将 B，C 接口的方法全部实现

```Go
//interface 是万能数据类型
//类型断言机制
func myFunc(arg interface{}) {
    value, ok = arg.(string)	//判断arg的类型
}
```



#### 6.Go 协程（Goroutine）和 channel

GMP  G : ` goroutine` 协程	P :` processor` 处理器	M : `thread` 线程

Go 协程介绍：

1). 主线程是一个物理线程，直接作用在 CPU 上，是重量级的，非常耗费 CPU 资源

2). 协程是从主线程开启的，是轻量级的线程，是逻辑态，对资源消耗相对小

3). Go 的协程机制是重要的特点，可以轻松的开启上万个协程；其他编程语言的并发机制一般是基于线程的，开启过多的线程，资源耗费非常大



调度器的设计策略	1.复用线程：work stealing 机制		hand off 机制		2.利用并行		3.抢占		4.全局 G 队列

channel 用于给 Go 协程通信

```go
make(chan Type)		
make(chan Type, capacity)		//这两种写法等价

channel <- value	//发送value到channel中
<- channel			//接收并将其丢弃
x := <- channel		//从channel中接收数据，并赋值给x
x, ok := <- channel	//功能同上，并检查 channel 是否关闭, ok 为 true 表示channel没有关闭，为false表示channel关闭
```



channel 用于各个 goroutine 之间进行通信

1.channel 本质是一个队列，数据是先进先出

2.线程安全，多个 goroutine 访问时，不需要加锁，就是说 channel 本身就是线程安全的

3.channel 是有类型的，一个 string 的 channel 只能存放 string 类型数据

4.channel 是引用类型，必须初始化后才能写入数据，即 make 后才能使用，给管道写入数据时，不能超过其容量



channel特点：当 channel 满时，再向里面写数据，就会阻塞；为空时，从里面取数据也会阻塞

channel 不像文件那样经常需要关闭，只有当你确实没有发送任何数据时，或者你想显示结束 range 循环之类的，采取关闭 channel 

关闭 channel 后，无法向 channel 再发送数据（引发 panic 错误后导致接收立即返回零值）

关闭 channel 后，可以继续从 channel 接收数据

对于 nil channel , 无论收发都会被阻塞

```go
var chanInt chan<- int  		// 声明为只读
chanInt := make(chan int, 3)

var chanInt <- chan int			// 声明为只写
```



使用 select 来解决从管道取数据的阻塞问题



有缓冲和无缓冲的 channel

单流程下一个 Go 只能监控一个 channel 的状态，select 可以完成监控多个 channel 的状态



通过 channel 实现同步和数据交互

```go
func person1() {
	Printer("Hello")
	ch <- 666 //给管道写入数据
}

func person2() {
	<- ch //从管道读取数据,如果管道为空便会阻塞
	Printer("world")
}

func Printer(str string) {
	for _, data := range str {
		fmt.Printf("%c", data)
		time.Sleep(time.Second / 2)
	}
	fmt.Println()
}

func main() {
    go person1()
	go person2()

	for {
	}
}
```



无缓冲的channel

无缓冲的通道是指在接收前没有能力保存任何值的通道

这种类型的通道要求发送goroutine和接收goroutine同时准备好，才能完成发送和接收操作. 如果两个goroutine都没有同时准备好，通道会导致先执行发送或接收操作的goroutine阻塞等待

```go
ch := make(chan int)	// 无缓冲通道
```



例1：

三个协程调用 `wait()` 等待，另一个协程调用 `Broadcast` 唤醒所有等待的协程

```go
var done = false

func read(name string, c *sync.Cond) {
	c.L.Lock()
	for !done {
		c.Wait()
	}
	log.Println(name, "starts reading")
	c.L.Unlock()
}

func write(name string, c *sync.Cond) {
	log.Println(name, "starts writing")

    time.Sleep(time.Second)		//模拟耗时,并等待前面的三个read协程都执行到 Wait()
	c.L.Lock()
	done = true
	c.L.Unlock()

	log.Println(name, "wakes all")
	c.Broadcast()
}

func main() {
    cond := sync.NewCond(&sync.Mutex{})

	go read("read1", cond)
	go read("read2", cond)
	go read("read3", cond)

	write("writer", cond)

	time.Sleep(time.Second * 3)
}
```

`done` 即互斥锁需要保护的条件变量

`read()`调用`wait()`等待通知，直到 done 为 true

`write()`接受数据，接收完成后，将 done 置为 true，调用`broadcast()`通知所有等待的线程



例2：

Gosched 函数让出时间片让子协程先执行

```go
go func() {
    for i := 0; i < 5; i++ {
        fmt.Println("go")
    }
}()

for i := 0; i < 2; i++ {
    runtime.Gosched()
    fmt.Println("hello")
}
```

Goexit 函数将立即终止当前的 goroutine 执行, 调度器确保所有已注册 defer 延迟调用被执行

GOMAXPROCES 用来设置可以并行计算的CPU核数的最大值，并返回之前的值



#### 7.反射 (reflect)

1.反射可以在运行时动态的获取变量的各种信息，比如变量的类型（type），类别（kind）

2.如果是结构体变量，还可以获取到结构体本身的信息（包括结构体的字段，方法）

3.通过反射，可以修改变量的值，可以调用关连的方法

4.Go 语言反射获取的函数类型只跟参数和返回值有关

```go
func reflectTest(b interface{}) {
	rVal := reflect.ValueOf(b)
    
    rVal.Elem().SetInt(20)
    //上面这一行就相当于
    //var num = 10
    //var b *int = &num
    //*b = 3
}

func main() {
    var num int = 10;
    reflectTest(&num)
    fmt.Println(num)
}
```



反射：使用反射来遍历结构体的字段，调用结构体的方法，并获取结构体标签的值

```go
type Monster struct {
	Name	string	`json:"name"`
	Age		int		`json:"monster_age"`
	Score	float32
	Sex		string		
}

func (s Monster) Print() {
	fmt.Println("---start---")
	fmt.Println(s)
	fmt.Println("---end----")
}

func(s Monster) GetSum(n1, n2 int) int {
	return n1 + n2
}

func(s Monster) Set(name string, age int, score float32, sex string) {
	s.Name = name
	s.Age = age
	s.Score = score
	s.Sex = sex
}

func TestStruct(a interface{}) {

	typ := reflect.TypeOf(a)		//获取reflect.Type类型
	val := reflect.ValueOf(a)		//获取reflect.Value类型
	kd := val.Kind()				//获取a对应的类别

	//如果传入的不是struct,则退出
	if kd != reflect.Struct {
		fmt.Println("expect struct")
		return
	}

	//获取该结构体有几个字段
	num := val.NumField()
	fmt.Printf("struct has %d fields\n", num)

	//遍历结构体所有字段
	for i := 0; i < num; i++ {
		fmt.Printf("Field %d: val = %v\n", i, val.Field(i))

		//获取到struct标签，注意需要通过reflect.Type来获取tag标签的值
		tagVal := typ.Field(i).Tag.Get("json")

		//如果该字段没有Tag标签就不显示
		if tagVal != "" {
			fmt.Printf("Field %d: tag = %v\n", i, tagVal)
		}
	}
	
	//获取到结构体有几个方法
	numOfMethod := val.NumMethod()
	fmt.Printf("struct has %d methods\n", numOfMethod)

	//Method()表示获取该方法，Call表示调用该方法
	//获取到第二个方法并调用,方法的顺序是按照函数名ASCII码的顺序排列的
	//所以调用的函数顺序默认为: GetSum(), Print(), Set() 三个方法
	val.Method(1).Call(nil)						

	//利用反射调用方法
	var params []reflect.Value
	params = append(params, reflect.ValueOf(10))
	params = append(params, reflect.ValueOf(40))
	res := val.Method(0).Call(params)				//传入的参数是 []reflect.Value
	fmt.Println("res =", res[0].Int())				//返回的结果是 []reflect.Value

}

func main() {
    var a Monster = Monster{
        Name: "Tom",
        Age: 24,
        Score: 99.99,
    }

    TestStruct(a)
}
```



#### 8.Go Module

Go Modules 是 Go 语言的依赖解决方案，发布于Go1.11，成长于Go1.12，丰富于Go1.13，正式于Go1.14推荐在生产中使用

Go Modules 目前集成在 Go 的工具链中，只要安装了 Go，自然而然也就可以使用 Go Modules 了，而 Go Modules 的出现也解决了 Go1.11 前的几个常见争议问题：

1.Go 语言长久以来的依赖管理问题

**2."淘汰" 现有的 GOPATH 的使用模式**

3.统一社区的其他的依赖管理工具（提供迁移功能）



GOMODULE 的用法

```bash
go mod init github.com/aceld/moudle_test	#初始化，这决定了以后其他人怎么导入这个包
go get #下载对应的包(这个下载的包实际上是在$GOPATH/bin/pkg/mod/下)
```



Go 语言提供了 GO111MOUDLE 这个环境变量来作为 Go Moudle 的开关，其允许设置以下参数：

auto：只要项目包含了 go.mod 文件的话启动 Go modules

on：启用 Go modules

off：禁用 Go modules

```bash
go env -w GO111MODULE=on	#启用 GO111MODULE,建议设置成auto选项，因为不是总是用的github上的包
go env -w GOPROXY=https://goproxy.cn,direct
```

GOPROXY：这个环境变量主要用于设置 Go 模块代理（Go moudle proxy），其作用是用于使 Go 在后续拉取模块版本时直接通过镜像站点来快速拉取，其实就是项目的第三方依赖库的下载地址，后面的 direct 参数表示指示 Go 回源到模块版本的源地址去抓取

GOSUMDB：用来校验拉取的第三方库是否是完整的，如果设置了 GOPROXY 就不用设置这个参数了

**注：**只要是镜像或者什么其他下载地址，全部换成 国内的镜像源

使用 Go Module 初始化非本地项目不要求一定在 `$GOPATH/src` 目录下，但是如果使用本地包还是需要在 /src 目录下



```go
//model包
var Name = "hello world"

//main包
import (
	"fmt"
    "model"
)
func main() {
    fmt.Println(model.Name)
}
```

这种情况如果 `go env -w GO111MODULE=on` 则无法使用 `GOPATH` 下的包



附：GO111MODULE

- =off，Go 命令会忽略 go.mod 文件，使用 GOPATH 搜索包

- =on，Go 命令会使用 go.mod 下的路径搜索包

- =auto, Go 命令会先搜索是否存在 go.mod 文件，不存在再使用 GOPATH 搜索包



go mod 命令

```go
go mod init			#生成 go.mod 文件
go mod download		#下载 go.mod 文件中指明的所有依赖
go mod tidy			#整理现有的依赖
go mod graph		#查看现有的依赖结构
go mod vendor		#到处项目所有的依赖到 vendor 目录
go mod verify		#校验一个模块是否被篡改过
go mod why			#查看为什么需要依赖某个模块
```



总结：在本地开发项目时，不同的包都要求是在 `$GOPATH/src` 下，这样才能应用，但是有的时候需要引用 Github 上的包，这时就有两种方式

1、直接下载下来放在 /src 目录下面，这样当然可以引用，但是十分繁琐，而不好管理

2、使用 GoMoudle，利用 go get 命令直接下载好需要的包（默认是在`$GOPATH/src/pkg/`下面），这样的好处是项目路径不必局限于一定要在 src/ 目录下，可以在任何目录，因为其会在 `pkg/` 目录下找我们下载好的包，这样操作方便也好管理



#### 9.接口（interface）

interface {} 空接口，称为万能类型

```go
func myFunc(arg interface{}) {
	fmt.Println("myFunc is called...", arg)

	val, ok := arg.(string)		//类型断言,如果是string类型则ok返回true，否则返回false
	if !ok {
		fmt.Println("arg is not string")
	} else {
		fmt.Printf("%T\n", val)
	}
}

func main() {
	book := Book{"Golang"}

	myFunc(book)
	myFunc(100)
	myFunc("abc")
	myFunc(3.14)
}
```



指针类型的结构体对象可以同时调用结构体值类型和指针类型对应的方法，而值类型的结构体对象只能调用值类型对应的方法

```Go
type People interface {
	Speak(string) string
}

type Student struct {}

//这里只能使用值传递，不能使用指针, 因为只有值类型实现接口 People 的方法，才能被定义为 People 类型
func (stu Student) Speak(think string) (talk string) {
	if think == "bitch" {
		talk = "hello"
	} else {
		talk = "world"
	}
	return 
}

func main() {
    var peo People = Student{}
	think := "bitch"
	fmt.Println(peo.Speak(think))
}
```



#### 10.结构体的序列化和反序列化

golang 中的结构体标签的使用

```go
type Movie struct {
	Title  string `json:"title"`
	Year   int    `json:"year"`
	Price  int    `json:"rmb"`
	Actors string `json:"actors"`
}

func main() {
    movie := Movie{"喜剧之王", 2000, 10, "zhangbozhi"}

	// 编码的过程, 结构体 -> json
	jsonStr, err := json.Marshal(movie)
	if err != nil {
		fmt.Println(err)
		return
	}

	// 需要使用 Printf 不能使用 Println
	// fmt.Println(jsonStr)
	fmt.Printf("%s\n", jsonStr)

	// 解码的过程, jsonStr -> 结构体
	myMovie := Movie{}
	err = json.Unmarshal(jsonStr, &myMovie)
	if err != nil {
		fmt.Println(err)
		return
	}

	fmt.Printf("%v\n", myMovie)
}
```





#### 11.Golang调试

通过 go tool nm 可以查看该文件中实现了哪些函数，nm会输出OBJ文件中定义或使用到的符号信息，通过 grep 命令过滤代码段符号对应的 T 标识，即可查看文件中实现的函数

Golang中的 *_test.go 全都是测试文件，其中的 BenchMark 测试要写在这个文件里面

Golang 的 recover 函数只能在 defer 函数中直接调用，不能通过另外的函数间接调用，这是语言实现层面的要求，不满足要求的 recover 调用，不会有任何效果

```go
// 正确:直接调用
func A() {
	defer B()
	panic("panicA")
}

func B() {
	p := recover()
	fmt.Println(p)
}

// 错误:间接调用
func A() {
	defer B()
	panic("panicA")
}

func B() {
	C()
}

func C() {
	p := recover()
	fmt.Println(p)
}
```





### 二、项目实战

一、Golang 实现即时通信系统项目

同属于一个包的文件不需要 import, Go 会自动链接



网络编程有两种：

1.TCP socket 编程，是网络编程的主流

2.b/s 结构的 http 编程，我们使用浏览器去访问服务器时，使用的就是 http 协议

如果把IP地址比做一个房子，那么端口就是出入这间房子的门，端口是通过端口号来标记的，端口号只有整数

0号是保留端口

1 - 1024 是固定端口，即被某些程序固定使用，程序员不要使用这些固定端口

如：22 端口是 SSH 远程登录协议，23 telent协议，21 ftp协议，25 smtp协议

1025 - 65535 是动态端口，程序员可以使用



附：127.0.0.1 和 localhost 和 本机IP三者的区别

答：**环回地址** 是主机用于向自身发送通信的一个特殊地址（也就是一个特殊的目的地址）

可以这么说：同一台主机上的两项服务若使用环回地址而非分配的主机地址，就可以绕开TCP/IP协议栈的下层。（也就是说：不用再通过什么链路层，物理层，以太网传出去了，而是可以直接在自己的网络层，运输层进行处理了）

IPv4的环回地址为：127.0.0.0 到 127.255.255.255 都是环回地址（只是有两个特殊的保留），此地址中的任何地址都不会出现在网络中

网络号为127的地址根本就不是一个网络地址（因为产生的IP数据报就不会到达外部网络接口中，是不离开主机的包）

当操作系统初始化本机的TCP/IP协议栈时，设置协议栈本身的IP地址为 127.0.0.1（保留地址），并注入路由表。当IP层接收到目的地址为127.0.0.1（准确的说是：网络号为127的IP）的数据包时，不调用网卡驱动进行二次封装，而是立即转发到本机IP层进行处理，由于不涉及底层操作。因此，ping 127.0.0.1一般作为测试本机TCP/IP协议栈正常与否的判断之一



**localhost** 首先是一个域名（如同：www.baidu.com），也是本机地址，它可以被配置为任意的 IP 地址（也就是说，可以通过 hosts 这个文件进行更改的），不过通常情况下都指向：
IPv4：表示  127.0.0.1

查看 Hosts 文件位于 `/etc/hosts`



**本机IP**：我们可以理解为本机有三块网卡，一块网卡叫做 loopback（虚拟网卡），一块叫做 ethernet（有线网卡），一块叫做 wlan（你的无线网卡）





二、尚硅谷的经典项目 --- 海量用户即时通讯系统

项目开发流程：需求分析	->	设计阶段	->	编码实现	->	测试阶段	->	实施

Golang 操作 Redis

连接池：1). 事先初始化一定数量的连接，放入到连接池	2). 当 Go 需要操作 Redis 时，直接从 Redis 连接池取出连接即可

3). 这样可以节省临时获取 Redis 连接的时间，从而提高效率





### 三、技术细节

只要是含有 main 函数文件的包全部设置 package main

函数的左括号 "{" 一定是与函数名在同一行的

##### 1.Go 中的 INT_MAX 和 INT_MIN

Go 中的 INT_MAX 和 INT_MIN 可以自己定义，也可直接使用 math 包中的 MaxInt

```Go
const UINT_MIN uint = 0			//无符号最小值
const UINT_MAX = ^uint(0)		//无符号最大值, Golang中取反用的是'^'不是'~'

//有符号最小值和最大值
const INT_MAX = int(^uint(0) >> 1)  //根据二进制补码,第一位为0,其余为1
const INT_MIN = ^INT_MAX

// Go中如果没有指定int数据类型，则默认为int64
res := 0
fmt.Print(unsafe.Sizeof(res))	//输出为8
```



```go
// golang中默认的int是8字节的
num := int(0)				// 这时num占用8字节
num := int8(0)				// 这时num占1字节

num = ^int8(0)				// 结果为-1
// 1.num写成补码表示为 0 0 0 0 0 0 0 0
// 2.取反可得 1 1 1 1 1 1 1 1
// 3.变成原码表示为 1 0 0 0 0 0 0 1 => -1
```



##### 2.从终端读取数据

三种读取方式总结：

```go
第一种：fmt.Scan
输入：读取以空白符分割的值返回到地址中进行修改，换行视为空白符
返回值：错误处理，返回值中有一个int类型的值是返回正确的数量，有一个err是错误的原因
var n1, n2 int
fmt.Scan(&n1, &n2)

第二种 fmt.Scanf
它与Scan比更加严谨，使用format读取空白符，在输入时也必须输入要输入的数据，适用场景学生输入账号，性别，而且必须要根据指定的方式输入，顺序也不可以改变
var n1, n2 int
fmt.Scanf("%d:%d", &a, &b)	//输入一定要 11:22，中间的冒号不能少

第三种 fmt.Scanln
Scanln 类似 Scan，但它在遇到换行时才停止扫描，最后一个数据后面必须有换行或者到达结束位置
使用场景就是只要换行就结束;返回错误和Scan一样
var n1, n2 int
fmt.Scanln(&a, &b)
```

以上三种方式一遇到空格便直接返回，都是以空格相隔



golang 使用简短方式声明变量，左侧必须要有一个新变量，变量也可以重复声明

```go
func main() {
	test1 := 0
	test1, test2:= 1, 2	
    
    test1 := 3 	//错误,因为左侧没有一个新变量
  	test1 = 3 	//正确
}
```



##### 3.append 函数的用法

```go
path = []int{1,2,3}
res = append(res, path)

path[0] = 10
res = append(res, path)		
//由于slice是通过引用来传递不是值传递，所以这时res结果为 [[10,2,3],[10,2,3]]

cp := make([]int, len(path))
copy(cp, path)
cp[0] = 10
res = append(res, cp)		//这时res结果为 [[1,2,3],[10,2,3]] 

// Leetcode第797题和第131题就是这个坑，回溯时一定要注意不要直接append原来的切片，一定要append原来切片的拷贝
res = append(res, path)		// path在后面可能会被更改，这就会导致res之前append进去的值会被更改

//方法一：
copy(cp,path)			// 1.创建一个副本
res = append(res, cp)	// 2.拷贝这个副本

//方法二：
res = append(res, append([]string{}, path...))		// 一步到位 
```

![](https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211129102149474.png) 

append 函数的用法注意（与其原始容量很有关系）

```go
var result = make([]int, 2, 100)  
var resultCap = make([]int, 2, 2) 

b := append(result, 1)		//由于result的容量很大，则在此基础上进行append不会导致新的切片的产生
c := append(resultCap, 3)   //而resultCap的容量有限，扩容会导致新的slice的产生

b[0] = 999    //切片b和result在底层的数组都是相同的，但是其长度是不同的      
c[0] = 999                        
fmt.Println(result, resultCap)  // 结果为[999, 0], [0, 0]
```

![](https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211129100955913.png) 

##### 4.字符串拼接的四种方法

```go
// 最推荐使用
func builderContact(n int, str string) string {
	var builder strings.Builder
	for i := 0; i < n; i++ {
		builder.WriteString(str)
	}
	return builder.String()
}

//第二种
func plusConcat(n int, str string) string {
	s := ""
	for i := 0; i < n; i++ {
		s += str
	}

	return s
}

//第三种
func SprintfConcat(n int, str string) string {
	s := ""
	for i := 0; i < n; i++ {
		s = fmt.Sprintf("%s%s", s, str)
	}
	return s
}

//第四种
func preByteConcat(n int, str string) string {
	buf := make([]byte, 0, n*len(str))
	for i := 0; i < n; i++ {
		buf = append(buf, str...)
	}
	return string(buf)
}
```



利用空结构体 + map 来实现集合 set

```go
type Set map[string]struct{}

func (s Set) Has(key string) bool {
	_, ok := s[key]
	return ok
}

func (s Set) Add(key string) {
	s[key] = struct{}{}
}

func (s Set) Delete(key string) {
	delete(s, key)
}

func main() {
    s := make(Set)
    s.Add("Tom")
    s.Add("Sam")
    fmt.Println(s.Has("Jack"))
}
```



##### 5.从终端输入

接受从终端的输入：想从终端输入一个带空格的字符串 "hello world"

使用下面这三种方法发现都只能读取到空格前面的字符串 "hello"

```go
var str string
fmt.Scan(&str)		
fmt.Println(str)		
fmt.Scanf("%s", &str)
```

使用第三方库即可

```go
reader := bufio.NewReader(os.Stdin)
res, _, err := reader.ReadLine()
if err != nil {
    fmt.Println("reader Readline err: ", err)
    return
}
str := string(res)
```



##### 6. 什么是逃逸分析

**答：**在 C 语言中，可以使用 `malloc` 和 `free` 手动在堆上分配和回收内存。Go 语言中，堆内存是通过垃圾回收机制自动管理的，无需开发者指定。那么，Go 编译器怎么知道某个变量需要分配在栈上，还是堆上呢？编译器决定内存分配位置的方式，就称之为逃逸分析(escape analysis)。逃逸分析由编译器完成，作用于编译阶段

函数传参何时传值何时传指针？

**答：**传值会拷贝整个对象，而传指针只会拷贝指针地址，指向的对象是同一个。传指针可以减少值的拷贝，但是会导致内存分配逃逸到堆中，增加垃圾回收(GC)的负担。在对象频繁创建和删除的场景下，传递指针导致的 GC 开销可能会严重影响性能。

一般情况下，对于需要修改原对象值，或占用内存比较大的结构体，选择传指针；对于只读的占用内存较小的结构体，直接传值能够获得更好的性能。



##### 7.golang中的方法

1.分析以下程序为什么能正常执行？

```go
type A struct {
    name string
}
func (a A) GetName() string {
    return a.name
}
func (pa *A) SetName() string {
    pa.name = "Hi! " + pa.name
    return pa.name
}
func main() {
    a := A{name: "eggo"}
    pa := &a
    
    fmt.Println(pa.GetName())
    fmt.Println(a.SetName())
}
```

**答：**编译期间，会把 `pa.GetName()` 这种方法调用转换成 ` (*pa).GetName()`，也就等价于执行 `A.GetName(*pa)`。而 `a.SetName()`会被转换成 `(&a).SetName()`，也相当于执行`(*A).SetName(&a)` 

![](https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211227110032255.png) 



2.编译器的语法糖

```go
type A struct {
	name string
}

func (a A) Name() string {
    a.name = "Hi! " + a.name
    return a.name
}

func NameOf(a A) string {
    a.name = "Hi " + a.name
    return a.name
}

func main() {
    a := A{name:"Eggo"}
    
    fmt.Println(a.Name())		// 1.编译器的语法糖，提供面向对象的语法
    fmt.Println(A.Name(a))		// 2.更贴近真实现实的写法，与普通函数调用没本质区别
    
    // golang 中的方法本质上就是普通函数，而接收者就是隐含的第一个参数
    t1 := reflect.TypeOf(A.Name)
    t2 := reflect.TypeOf(NameOf)
    fmt.Println(t1 == t2)		// 输出true
}
```



##### 8.其他

判断下面程序的输出

```go
slice := []int{0, 1, 2, 3}
m := make(map[int]*int)

for k, v := range slice {
    m[k] = &v
}

for k, v := range m {
    fmt.Println(k, *v)
}
```



### 四、Go 源码

##### 1.寻找三个数的中位数

```go
// medianOfThree moves the median of the three values data[m0], data[m1], data[m2] into data[m1].
func medianOfThree(data Interface, m1, m0, m2 int) {
	// sort 3 elements
	if data.Less(m1, m0) {
		data.Swap(m1, m0)
	}
	// data[m0] <= data[m1]
	if data.Less(m2, m1) {
		data.Swap(m2, m1)
        // 因为上面的mo<=m1, 但是现在m1和m2交换了, 所以才有 m0 < m2, m1 < m2
		// data[m0] <= data[m2] && data[m1] < data[m2]
		if data.Less(m1, m0) {
			data.Swap(m1, m0)
		}
	}
	// now data[m0] <= data[m1] <= data[m2]
}

// 可以拿 (m0, m1, m2) = (5, 4, 3) 举例
```



##### 2.返回 [log_2(n)]

```go
func stacklog2(n uintptr) int {
	log2 := 0
	for n > 1 {
		n >>= 1
		log2++
	}
	return log2
}
```



##### 3.大小写转换

摘选自 Nginx 源码

```c
#define ngx_tolower(c)      (u_char) ((c >= 'A' && c <= 'Z') ? (c | 0x20) : c)
#define ngx_toupper(c)      (u_char) ((c >= 'a' && c <= 'z') ? (c & ~0x20) : c)
```





### 五、面试题

##### 1.函数调用时参数的入栈顺序

**答：**

1、首先：在函数调用过程中，最先入栈的是调用函数处的下一条指令的地址，这样函数调用完成后返回到该地址继续执行，这个地址是很重要的，然后才是函数的参数，函数的内部局部变量。调用结束，将栈内容一一出栈，最后栈顶指向了开始存储的指令地址，主函数继续从这里开始运行。

2、其次：函数调用参数的入栈顺序和C语言支持变长参数有关，比如 printf 函数就支持变长参数，也即 void printf(const char *fmt，……)，这个时候并不知道参数有多少个，如果从左向右入栈，那么 fmt 就在栈底，该参数的位置无法通过栈顶指针偏移来确定，因为不知道栈顶和栈底之间有多少个参数，大小是多少，无法确定，但是，如果从右向左入栈的话，fmt 参数就在栈顶的位置，通过这个固定的普通参数就可以通过偏移逐一寻址到后续参数的地址

总结：被调用函数是通过栈指针加上偏移值这样相对寻址的方式来定位到自己的参数和返回值的，这样由下至上正好先找到第一个参数，再找到第二个参数。所以参数和返回值采用由右至左的入栈顺序比较合适



##### 2.切片的扩容规则

**答:** 当一个切片的容量无法存储更多元素时，切片会自动扩容，它会生成一个容量更大的新切片，然后把原切片的元素和新元素一起拷贝到新切片中。

在原切片长度小于 1024 时，新切片的容量会按照原切片的 2 倍扩容，否则，新切片的容量会按照原切片的 1.25 倍扩容，此时需要注意的是，如果新切片的容量按照原切片的 1.25 倍扩容一次仍然无法存储新元素时，将会不断按照原切片的 1.25 倍扩容，直到新切片的容量可以存储原切片的元素和新元素为止。一般最终扩容后的新切片，它的容量会大于或等于原切片的容量。

需要注意的是，当切片的零值是 nil 时，切片此时还没有指向底层数组。但是切片的零值是可用的，当使用 append 向零值切片追加元素时，将会先给切片分配一个底层数组。

切片扩容实际是创建一个新的底层数组，把原切片的元素和新元素一起拷贝到新切片的底层数组中，原切片的底层数组将会被垃圾回收。

**注意：切片的容量可以根据元素的个数的增多自动扩容，但是不会根据元素的个数的减少自动缩容**



