### 一、SHELL

#### 1.shell基础

##### 1.SHELL编程

脚本运行的几种方式  (脚本用处：批量的执行命令)

1. 添加可执行权限后 `chmod a+x sample.sh`，直接 ./sample.sh

2. 不需要添加可执行权限，直接 . sample.sh  或者指定解析器执行 /bin/bash sample.sh  或者 source sample.sh



##### 2.shell数据类型

shell 里面的所有东西都是字符串，即 shell 只有一种数据类型字符串

有两种类型的 shell 变量，变量都是键值对的形式，等号两边不能有空格

- 环境变量：环境变量可以从父进程传给子进程，因此 shell 的环境变量可以从当前 shell 进程传给 fork 出来的子进程；用printenv命令可以查看显示当前shell进程的环境变量

- 本地变量：本地变量只存在于当前shell进程，用set命令就可以显示当前shell进程中定义的所有变量 ( 包括本地变量和环境变量 ) 和函数



被双引号括住的内容，将被视为单一字符串；它防止通配符扩展，但允许变量扩展；这点与单引号的处理方式不同

":" 是一个特殊的命令，称为空命令，该命令不做任何事；但 Exit Status 总是为真；

和 C 语言类似，"\"在shell中被用作转义字符，用于去除紧跟其后的单个字符的特殊含义(回车除外)，换句话说，紧跟其后的字符取字面值





##### 3.条件测试

命令 test 和 [ 属于条件测试命令；test 和 [ 可以互换，如 test -e /etc/passwd  ==   [ -e /etc/passwd ]

| [ -f FILE ] 如果FILE存在且是一个普通文件则为真 | [ -d DIR ] 如果DIR存在且是一个目录则为真     | [ -z STRING ] 如果STRING的长度为零则为真        |
| ---------------------------------------------- | -------------------------------------------- | ----------------------------------------------- |
| **[ -n STRING ] 如果STRING的长度 >0 则为真**   | **[ STR1 = STR2 ] 如果两个字符串相同则为真** | **[ STR1 != STR2 ] 如果两个字符串不相同则为真** |

[ ARG1 OP ARG2 ] 如果ARG1或者ARG2应该为整数或者取值为整数的变量，OP可以为 -eq(等于)，-ne(不等于)，-lt(小于)，-le(小于等于)，-gt(大于)，-ge(大于等于)

echo $? 返回上一个进程退出时的返回值；如果为真返回0，为假返回1（注：与C语言的返回恰好相反）

作为一种好的编程习惯，应该总是把变量取值放在双引号之中



附：C语言 if else 和switch的区别

**答：**if else 语句更多用于模糊匹配也就是匹配的是一个范围，而 switch 语句是精确匹配，匹配的是单个字符



esac 表示 case 语句块的结束；shell 脚本的 case 语句可以匹配字符串和 Wildcard，每个匹配分支可以有若干条命令，末尾必须要有 ";;" 结尾代表结束

break[n] 可以指定跳出几层循环

注："$(ls)" ls 加上括号表示将 ls 当作命令执行；"$FILENAME" 表示取 FILENAME 变量里面的值

shell脚本调式方法：

-n  读一遍脚本中的命令但不执行，用于检查脚本中的语法错误

-v  一边执行脚本，一边将执行过的的脚本命令打印到标准错误输出

-x  提供跟踪执行信息，将执行的每一条命令和结果依次打印出来





##### 4.shell脚本命令

**内建命令：**用户在命令行输入命令后，一般情况下 shell 会 fork 一个子进程并执行 exec 命令，但是 shell 的内建命令例外，执行内建命令相当于调用 shell 进程的一个函数，并不创建新的进程；

之前学过的 cd, alias, umask 以及 exit 命令都是内建命令，凡是用 which 命令查不到程序文件所在位置的命令都是内建命令

如 export，shift, if, eval, [ , for, while 等都是内建命令；内建命令虽然不会创建新的进程，但是也会有 Exit Status，执行结束后也会有一个状态码，也可以用特殊变量 $? 读出



echo 命令

```bash
#echo命令
echo "Hello" >> test.txt	# ">>" 代表再原有文本后追加
echo "world" > test.txt		# ">" 代表覆盖原有文本
```



"sh -c" 命令：它可以让 bash 将一个字串作为完整的命令来执行

```bash
/bin/sh -c 'echo "hahah" >> test.asc'
```



bash中的其他特殊符号

单引号 ' '：单引号内的所有特殊符号，如 $ 和 ` 都没有特殊含义

双引号 " "：双引号内中特殊符号都没有特殊含义，但是 '$', '\`', '\\' 是例外；'$'代表调用变量的值，'`'代表引用命令，'\\'代表转义字符

$( ) ：和反引号作用一样，代表引用系统命令



cut 命令：cut [选项] 文件名

-f 列号：提取第几列     -d 分隔符：提取第几行



printf命令：

```bash
printf '%s %s %s\n' 1 2 3 4 5 6
# 输出结果为 1 2 3
#  		   4 5 6
```

print 命令与 printf 的区别是 print 会默认输出一个换行符，但是 printf 是 Linux 系统命令，print 不是

而 awk 命令中既可以使用 print 也可以使用 printf



排序命令 sort：

sort [选项]  文件名

-f  忽略大小写			-n  以数值型进行排序，默认使用字符串型排序

-r  反向排序			   -t  指定分隔符，默认分隔符是制表符



统计命令 wc：

wc [选项]  文件名

-l  统计行数	-w 统计单词数	-m 统计单词数



例1：shell 脚本(每隔一秒向文本文件输入当前日期)

```bash
touch hello.txt
while true
do echo $(date) >> hello.txt
sleep 1
done
```



##### 5.Linux常用命令

1.Linux下 find 命令的用法

```bash
find ./ -name *.mp3   			#按名字查找, -name
find ./ -type(f/d/p/c/b/s/l) 	#按类型查找, -type
find /home/chao	-size +3M -size -7M		#在home下面的chao目录下查找文件大于3M小于7M的文件
find /home/chao -size +34k -size -89k	#这里的kb不能写成大写的k
```



2.EOF（End Of File） 的用法，EOF 通常配合 cat 或者 tee 使用

```bash
cat >> test.txt << EOF	# 开始
...						# 要向文件输入的内容，注意cat后面的">>"表示追加，改成 ">" 表示覆盖原有内容
EOF						# 结束

tee -a test.text << EOF	# -a 表示在文件末尾追加
...
EOF

# 将 echo 命令的输出作为 tee 命令的输入, 但是 tee 会将输入打印到终端，所以这里将tee的默认终端输出重定向到黑洞文件
echo "hello world" | tee hello.txt > /dev/null
```







#### 2.正则表达式

正则表达式用来在文件中匹配符合条件的字符串，正则是包含匹配；grep，awk，sed 等命令都可以支持正则表达式

通配符用来匹配符合条件的文件名，通配符是完全匹配 (精确匹配)；ls，find，cp这些命令不支持正则表达式，所以只能使用shell自己的通配符来进行匹配

通配符可以看作是正则表达式的一部分



**通配符**字符类：

\* 匹配0个或者多个任意字符

? 匹配单个任意字符

[若干字符] 匹配方括号中任意一个字符

"-" 在[ ]括号内表示字符范围

"^"位于[ ]括号的开头，匹配除括号中的字符之外的任意一个字符



**正则中**数量限定符：

？表示紧跟在它前面的单元应该匹配 0次或者1次

\+ 表示紧跟在它前面的单元应该匹配 1次或者多次

\*  ............................................................ 0次或者多次

{N} .......................................................... 精切匹配N次

{N, } ........................................................ 至少N次

{, N} ........................................................ 至多N次

{M, N} .................................................... 出现[M, N] 次



位置限定符

| ^ 匹配行首的位置                | $ 匹配行尾的位置                  | \< 匹配单词开头的位置 | \> 匹配单词结尾的位置 |
| ------------------------------- | --------------------------------- | --------------------- | --------------------- |
| **\b 匹配单词开头或结尾的位置** | **\B 匹配非单词开头和结尾的位置** |                       |                       |



正则种类：Basic 正则和 Extended 正则

使用 grep 用的基础正则；使用 egrep 用的是扩展正则

两者的区别在于对于基础正则 "( )", "{ }"小括号和中括号都需要转义



正则表达式匹配IP地址

```bash
egrep "^((25[0-5]|2[0-4][0-9]|1[0-9][0-9]|[1-9][0-9]|[0-9])\.){3}(25[0-5]|2[0-4][0-9]|1[0-9][0-9]|[1-9][0-9]|[0-9])"  file
```



#### 3.sed的使用

sed 意为流编辑器（Stream Editor），在 shell 脚本和 Makefile 中作为过滤器使用非常普遍，也就是把前一个程序的输出作为后一个程序的输入，经过一系列编辑命令转换成另外一种格式输出；sed 和 vi 都源于早期 Unix 的ed工具，所以很多 sed 命令和 vi 的末行命令都是相同的

sed [选项]  '[动作]'  文件名       (动作需要加上引号)

格式：

sed  参数  '脚本语句'  待操作文件

sed  参数   -f  '脚本文件'  待操作文件

选项参数：

-n  一般 sed 命令会把所有数据输出到屏幕，如果加上此选项，则只会把经过sed命令处理的行输出到屏幕

-e  允许对输入数据应用多条 sed 命令编辑

-i  用 sed 的修改结果直接修改读取数据的文件，而不是由屏幕输出

sed 以行为单位处理文件，awk 比 sed 强的地方在于不仅能行为单位，还能以列为单位处理文件

sed常用参数：

a,  append  追加  sed "2a xxxx" test.sh  在第2行后另起一行追加

i,  insert     插入  sed "2i xxx" test.sh  在第2行插入

d,  delete     删除  sed "2,4 d" test.sh  删除第2到4行

s,  substitution  替换  sed "s/echo/printf/g" 将 echo 替换成 printf，后面的 /g 表示如果一行存在多个则全部替换

如：sed 's/要被替换的字符串/新的字符串/g' (**后面这个g不加也可以**)

s是字符串替换，c是行替换

sed中的 '[ ]' 表示取反，对方括号内的内容取反



例1：提取IP地址

```bash
# 文本IP如下
inet addr:192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0  

cat IP | sed 's/^.*addr://' | sed 's/Bcast:*$'
```





#### 4.awk的使用

awk '条件1 {动作1} 条件2 {动作2} ...'  文件名

awk 缺省的行分隔符是换行，缺省的列分隔符是连续的空格和Tab

使用格式：awk 参数  '脚本语句'  待操作文件

awk -f  '脚本文件'  待操作文件

awk 中的 "$0" 表示打印所有列

BEGIN 在所有命令执行之前先执行BEGIN后的语句；END 在所有命令执行完毕再执行END后的语句



### 二、Makefile

**Makefile 中的注释建议全部卸载行首上一行，不要写在行尾，Makefile 对空格很敏感**

C++头文件的搜索路径在  /usr/include 目录下





##### 1.静态库安装方法及其使用

1.将源文件编译成 .o 文件

![](https://raw.githubusercontent.com/BoomChao/Windows_Figure/main/20211128194048.png)  

头文件放在include文件夹下，源文件放在 src 文件夹下

```c
g++ *.c -c -I../include		//生成*.o文件
ar rcs libMyCalc.a *.o		//打包成静态库MyCalc,之后将静态库移动到lib目录下即可
```

2.发布的时候只需要提供库和头文件即可

![](https://raw.githubusercontent.com/BoomChao/Figure/master/Screenshot%20from%202021-11-17%2021-05-58_1.png)  

```c
gcc main.c lib/libMyCalc.a -o myApp -I./include		//链接库生成可执行文件 myApp
```



设置编译器的编译选项

```bash
main : main.cc
	g++ main.cc -o main -std=c++17		#1.使用C++17标准编译
	g++ main.cc -fno-elide-constructors -o main		#2.禁止编译器优化
```



##### 2.gcc 常见参数

```bash
1. gcc -E source_file.c
-E，只执行到预编译。直接输出预编译结果。

2. gcc -S source_file.c
-S，只执行到源代码到汇编代码的转换，输出汇编代码。

3. gcc -c source_file.c
-c，只执行到编译，输出目标文件。

4. gcc (-E/S/c/) source_file.c -o output_filename
-o, 指定输出文件名，可以配合以上三种标签使用。
-o 参数可以被省略。这种情况下编译器将使用以下默认名称输出：
-E：预编译结果将被输出到标准输出端口（通常是显示器）
-S：生成名为source_file.s的汇编代码
-c：生成名为source_file.o的目标文件。
无标签情况：生成名为a.out的可执行文件。

5. gcc -g source_file.c
-g，生成供调试用的可执行文件，可以在gdb中运行。由于文件中包含了调试信息因此运行效率很低，且文件也大不少。
这里可以用strip命令重新将文件中debug信息删除。这是会发现生成的文件甚至比正常编译的输出更小了，这是因为strip把原先正常编译中的一些额外信息（如函数名之类）也删除了。用法为 strip a.out

6. gcc -s source_file.c
-s, 直接生成与运用strip同样效果的可执行文件（删除了所有符号信息）

7. gcc -O source_file.c
-O（大写的字母O），编译器对代码进行自动优化编译，输出效率更高的可执行文件。
-O 后面还可以跟上数字指定优化级别，如：
gcc -O2 source_file.c
数字越大，越加优化。但是通常情况下，自动的东西都不是太聪明，太大的优化级别可能会使生成的文件产生一系列的bug。一般可选择2；3会有一定风险

8. gcc -Wall source_file.c
-W，在编译中开启一些额外的警告（warning）信息。-Wall，将所有的警告信息全开。

9. gcc source_file.c -L/path/to/lib -lxxx -I/path/to/include
-l, 指定所使用到的函数库，本例中链接器会尝试链接名为 libxxx.a 的函数库
-L，指定函数库所在的文件夹，本例中链接器会尝试搜索 /path/to/lib 文件夹
-I, 指定头文件所在的文件夹，本例中预编译器会尝试搜索 /path/to/include 文件夹

PKG_CONFIG_PATH：用来指定pkg-config用到的pc文件的路径，默认是 usr/lib/pkgconfig, pc文件是文本文件，扩展名是.pc，里面定义开发包的安装路径，Libs参数和Cflags参数等等。

CC：用来指定c编译器。

CXX：用来指定c++编译器。

LIBS：跟上面的--libs作用差不多。

CFLAGS: 指定C编译器的编译选项
CXXFALGS: 用来指定C++编译器的编译选项

CC，CXX，LIBS，CFLAGS手动编译时一般用不上，在做configure时有时用到，一般情况下不用管。

LDFLAGS是选项，LIBS是要链接的库。都是喂给ld的，只不过一个是告诉ld怎么吃，一个是告诉ld要吃什么。
网上不难搜索到上面这段话。不过“告诉ld怎么吃”是什么意思呢？
LDFLAGS = -L/var/xxx/lib -L/opt/mysql/lib
LIBS = -lmysqlclient -liconv
```

 

Makefile中的 % 表示通配符，而 * 表示任意字符

举例，%.o表示匹配所有的.o文件，注意是用于匹配的；*.o是表示所有的.o文件。

用途方面：前者一般用作目标，后者一般用作删除



反引号： \`date`  等同于  $(date)

在Linux中反引号起着命令替换的作用；将反引号中的字符串做为命令来执行

如下，shell会执行反引号中的date命令，把结果插入到echo命令显示的内容中

shell中有两种方法作命令替换，除了反引号还可以用 $(…) 结构括起来，$(…) 格式受到 POSIX 标准支持，也利于嵌套；但反引号内不能再引用反引号，而 $() 中可以引用反引号

```bash
# notdir 用于去掉文件的绝对路径,只保留文件名
# basename 取前缀函数(前缀部分指的是文件名中最后一个'.'号之前的部分)
# addsuffix : (addsuffix <prefix>, <name1 name2 ...>) 将前缀<prefix>加到name序列中每一个元素前面

DIR = ./MyTest
SOURCES = $(DIR)/test.cc

t1:
	@echo $(notdir $(SOURCES))
	@echo $(basename $(notdir $(SOURCES)))
	@echo $(addsuffix .o, $(basename $(notdir $(SOURCES))))

# 输出结果如下：
# test.cc
# test
# test.o
```



Makefiel将编译后的目标文件放在指定文件夹下

```makefile
OBJ := ./MyTest

main:$(OBJ)/main.o
	g++ $^ -o $@ 

$(OBJ)/%.o:%.cc
#	g++ -c main.cc -o main.o		# 注意：这里写成这样会出错，这样是默认main.cc在当前目录，而不是上面指定的目录
	g++ -c $^ -o $@					# 建议以后全部写成这样

clean:
	rm -rf $(OBJ)/*.o
```



对于如下编译

```bash
![image-20211217102210790](C:\Users\12458\Desktop\Typora\typora-user-images\image-20211217102210790.png)main:
	g++ main.cc -L/home/chao/Documents/Software/petsc-3.13.4/arch-linux-c-debug/lib \
				-Wl,-rpath,/usr/lib/gcc/x86_64-linux-gnu/7 -L/usr/lib/gcc/x86_64-linux-gnu/7 \
				-Wl,-rpath,/usr/lib/x86_64-linux-gnu -L/usr/lib/x86_64-linux-gnu -Wl,-rpath,/lib/x86_64-linux-gnu\
				-L/lib/x86_64-linux-gnu -lflapack -lfblas -lpthread  -lgfortran -lm -o main 


# -rpath是指示程序运行时链接指定库的路径
# -L是指示编译时搜索库文件的目录
# -lpthread表示在上面指定的lib路径中搜索pthread动态库文件 
```





**pkg-config 的用法**

来源：由于编译GTK+程序所需要的连接的寻找的信息非常的多。非GTK+的开发者并不一定能够正确的指定所有需要追加到 gcc 中的选项。所以GTK+利用了一个专门的工具来生成编译GTK+程序所需要追加的选项，也就是 pkg-config

Pkgconfig提供了下面几个功能：

1.         检查库的版本号。如果所需要的库的版本不满足要求，它会打印出错误信息，避免链接错误版本的库文件
2.         获得编译预处理参数，如宏定义，头文件的位置
3.         获得链接参数，如库及依赖的其它库的位置，文件名及其它一些连接参数
4.         自动加入所依赖的其它库的设置

pkgconfig 并非精灵，可以凭空得到以上信息。事实上，为了让 pkgconfig 可以得到这些信息，要求库的提供者，提供一个 *.pc* 文件

pkg-config 工具所生成的内容是在相关软件包编译时产生，默认放置在 /usr/lib/pkgconfig 目录和 /usr/local/lib/pkgconfig/ 下，以 xxx.pc 的文本文件形式存在，实际上是一个解决软件包之间编译依赖关系的一个配置记录工具 ，如果需要放置到其他目录，需要指定环境变量 PKG_CONFIG_PATH

```bash
pkg-config --cflags --libs gtk+-2.0 的执行结果如下（环境是Qomo Linux 1.2):
[xxxxxxxxxxxx]
```

在bash编程中，用反引号引起的内容实际效果是将反引号中的命令执行，并将输出结果作为脚本中的内容解释执行；

所以 gcc base.c -o base \`pkg-config --cflags --libs gtk+-2.0`实际上等价于

```bash
gcc base.c -o base [xxxxxxxxxxxx]
```



##### 3.报错处理

Makefile报错如下：第123行首或者行尾多个空格或者tabl缩进 

![](https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211217102210790.png) 





### 三、CMake的使用

CMake是开源、跨平台的构建工具，可以让我们通过编写简单的配置文件去生成本地的Makefile，这个配置文件是独立于运行平台和编译器的，这样就不用亲自去编写 Makefile了，而且配置文件可以直接拿到其它平台上使用，无需修改，非常方便

```cmake
#指定CMake编译最低要求版本
CMAKE_MINIMUM_REQUIRED(VERSION 3.14)

#给项目命名
PROJECT(demo)

#查找liblog库，并将路径存为变量libname
find_library(libname log)

#收集c/c++文件并赋值给变量SRC_LIST_CPP  ${PROJECT_SOURCE_DIR}代表当前项目目录
FILE(GLOB SRC_LIST_CPP ${PROJECT_SOURCE_DIR}/src/*.cpp)
FILE(GLOB SRC_LIST_C ${PROJECT_SOURCE_DIR}/src/*.c)

#指定头文件目录
INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR}/include)
#指定链接库文件目录
LINK_DIRECTORIES(${PROJECT_SOURCE_DIR}/lib)

# 指定源文件目录
AUX_SOURCE_DIRECTORY(${CMAKE_SOURCE_DIR}/src/main/cpp SRC_LIST)

#指定生成库文件的目录
SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
#取变量 SRC_LIST_CPP 与 SRC_LIST_C 指定生成 libmyprint 动态库   默认生成静态库  SHARED指定生成库类型为动态库
ADD_LIBRARY(myprint SHARED ${SRC_LIST_CPP} ${SRC_LIST_C})

#将 hello.cpp 生成可执行文件 hello 
ADD_EXECUTABLE(hello ${SOURCE})

#指定目标hello 需要链接库libmyprint, 这个库如果在默认目录找不到就会到 add_library 添加的目录中去找
TARGET_LINK_LIBRARIES(hello myprint)
```

googletest 头文件在 /usr/include 下面，静态库在 /usr/lib下面	gtest 安装在 /usr/src/gtest



