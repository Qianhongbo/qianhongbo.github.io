---
title: 'gcc makefile c++'
date: 2022-12-06 14:04:44
categories: 
- c++
tags: 
- c++
---

## gcc

### 概念

`gcc`是`GNU Compiler Collection`（就是GNU编译器套件），也可以简单认为是编译器，它可以编译很**多种编程语言**（包括`C`、`C++`、`Objective-C`、`Fortran`、`Java`等等）。

### 特点

- 当你的程序只有**一个**源文件时，直接就可以用gcc命令编译它。

- 但是当你的程序包含很**多个**源文件时，用gcc命令逐个去编译时，你就很容易混乱而且工作量大

## make

所以出现了`make`工具，make工具可以看成是一个智能的批处理工具，它本身并没有编译和链接的功能，而是用类似于批处理的方式—通过调用`makefile`文件中用户指定的命令来进行编译和链接的。

### makefile

`makefile`简单的说就像一首歌的乐谱，make工具就像指挥家，指挥家根据乐谱指挥整个乐团怎么样演奏，make工具就根据makefile中的命令进行编译和链接的。

makefile命令中就包含了调用gcc（也可以是别的编译器）去编译某个源文件的命令。

makefile在一些简单的工程完全可以人工手下，但是当工程非常大的时候，手写makefile也是非常麻烦的，如果换了个平台makefile又要重新修改。

### cmake

这时候就出现了`cmake`这个工具，cmake就可以更加简单的生成makefile文件给上面那个make用。当然cmake还有其他功能，就是可以跨平台生成对应平台能用的makefile，你不用再自己去修改了。

可是cmake根据什么生成makefile呢？它又要根据一个叫CMakeLists.txt文件（学名：组态档）去生成makefile。

## 总结

CMake ———> makefile （包含调用gcc的命令）————> make工具 —————> 编译链接源文件

## 语法

```shell
gcc (选项) (参数)
```

### 选项

```shell
-o：指定生成的输出文件；
-E：仅执行编译预处理；
-S：将C代码转换为汇编代码；
-wall：显示警告信息；
-c：仅执行编译操作，不进行连接操作。
-l：用来指定程序要链接的库，-l参数紧接着就是库名
-I：寻找头文件的目录
```

### 参数

C源文件：指定C语言源代码文件。

### 实例

**常用编译命令选项**

假设源程序文件名为test.c

**无选项编译链接**

```shell
gcc test.c
```

将 `test.c` 预处理、汇编、编译并链接形成可执行文件。这里未指定输出文件，默认输出为 `a.out`。

**选项 -o**

```shell
gcc test.c -o test
```

将 `test.c` 预处理、汇编、编译并链接形成可执行文件 `test`。`-o` 选项用来指定输出文件的文件名。

**选项 -E**

```shell
gcc -E test.c -o test.i
```

将 `test.c` 预处理输出 `test.i` 文件。

**选项 -S**

```shell
gcc -S test.i
```

将预处理输出文件 `test.i` 汇编成 `test.s` 文件。

**选项 -c**

```shell
gcc -c test.s
```

将汇编输出文件 `test.s` 编译输出 `test.o` 文件。

**无选项链接**

```shell
gcc test.o -o test
```

将编译输出文件 `test.o` 链接成最终可执行文件 `test`。

**选项 -O**

```shell
gcc -O1 test.c -o test
```

使用编译优化级别1编译程序。级别为1~3，级别越大优化效果越好，但编译时间越长。

**多源文件的编译方法**

如果有多个源文件，基本上有两种编译方法：

假设有两个源文件为 `test.c` 和 `testfun.c`

**多个文件一起编译**

```shell
gcc testfun.c test.c -o test
```

将 `testfun.c` 和 `test.c` 分别编译后链接成 `test` 可执行文件。

**分别编译各个源文件，之后对编译后输出的目标文件链接。**

```shell
gcc -c testfun.c    #将testfun.c编译成testfun.o
gcc -c test.c       #将test.c编译成test.o
gcc testfun.o test.o -o test   #将testfun.o和test.o链接成test
```

以上两种方法相比较，第一中方法编译时需要所有文件重新编译，而第二种方法可以只重新编译修改的文件，未修改的文件不用重新编译。

**加载动态链接库**

```shell
gcc hello.c -lpthread -o hello
```

**手动添加文件头路径**

```shell
gcc hello.c -lpthread -I /lib64/ -o hello
```
