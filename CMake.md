## CMake

cmake提供了一个命令可以把指定目录下所有的源文件存储在一个变量中，这个命令就是 **aux_source_directory(dir var)**

```cmake
aux_source_directory(. SRC_LIST) #第一个参数dir是指定目录，第二个参数var是用于存放源文件列表的变量
```

aux_source_directory( ) 也存在弊端，它会把指定目录下的所有源文件都加进来，可能会加入一些我们不需要的文件，此时我们可以使用 set 命令去新建变量来存放需要的源文件

```cmake
set( SRC_LIST
	 ./main.c
	 ./testFunc1.c
	 ./testFunc.c)
```



include_directories 该命令是用来向工程添加多个指定头文件的搜索路径，路径之间用空格分隔

```cmake
include_directories(./testFunc ./testFunc1)		#添加头文件的搜索路径
```

