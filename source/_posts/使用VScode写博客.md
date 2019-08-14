---
title: 使用VSCode写博客
date: 2019-08-13 11:24:09
categories: 
- 学习笔记
tags: 
- VScode
---

# 使用原因

偶然之间看到一篇文章推荐使用VSCode，对于它我也是早有耳闻，一个开源的代码编辑器，但是之前觉得麻烦，觉得自己已经有了Visual Studio 2017就没必要再下一个代码编辑器了，当时以为VSCode不带有编译功能。

昨晚没事干，就下了个尝试一下啊，果然耳目一新，觉得从颜值也好，使用上也好都很舒服，但在配置的时候也遇到了问题，研究了好久才终于解决，特此记录！

# 步骤

- 从官网安装VSCode
- 安装MinGW64,并将bin文件添加到Path路径中
- 安装插件
- 成功运行代码！

# 具体过程

## 从官网安装VSCode

过程比较简单，从Google搜索VSCode进入官方网站，下载Windows的版本，运行即可。

## 安装MinGW64

由于想在VSCode里跑C代码，最开始下了个C/C++的扩展程序，以为就可以用`Fn+F5`正常运行，实则不然，遇到报错！
```
提示这个内容：lauch:program does not exist
```
并要求我修改`launch.json`文件，可是我怎么修改也不管用...
逐渐开始进入崩溃状态，想要直接写在这**的VSCode...
可是作为一个颜控的我沉迷于VSCode的颜值无法自拔，决定再试试...

最终发现问题出现在没有安装MinGW64无法调试，于是搜索教程，前往官网下载，并将bin文件的目录添加到`path`路径中去。

并最终用`gcc -v`测试是否安装成功，要注意在`cmd`里测试，不要像我一样，在`cmder`里怎么测试都不成功...

安装具体教程点击这个[链接](https://www.cnblogs.com/findumars/p/8289454.html)

## 安装插件

VSCode不安装插件是没有灵魂的...

我目前还不是很懂，也只是安装了一下几个插件

- C/C++
- C++ Intellisense
- Chinese Language
- Code Runner
- Seti-theme

## 运行代码

最终用配置好的环境跑了一下`hello，world`成功编译！
<font color="#006600">
## 问题补充（修改于19.8.14）

再次出现问题！之前成功编译只是通过了代码右上角的“播放”按键，也就是插件`Code Runner`，然而`F5`依然无法通过！

继续查阅资料，查到一篇比较详细的教程，[地址请点击](https://blog.csdn.net/bat67/article/details/76095813#commentBox)

简单来讲就是还要修改`.vscode`文件夹里的`launch.json`和`tasks.json`文件。

而我是将`launch.json`修改为，其中`gdb.exe`的路径需要自己进行修改一下
</font><br /> 
```java
{  
    "version": "0.2.0",  
    "configurations": [  
        {  
            "name": "(gdb) Launch", // 配置名称，将会在启动配置的下拉菜单中显示  
            "type": "cppdbg",       // 配置类型，这里只能为cppdbg  
            "request": "launch",    // 请求配置类型，可以为launch（启动）或attach（附加）  
            "program": "${workspaceFolder}/${fileBasenameNoExtension}.exe",// 将要进行调试的程序的路径  
            "args": [],             // 程序调试时传递给程序的命令行参数，一般设为空即可  
            "stopAtEntry": false,   // 设为true时程序将暂停在程序入口处，一般设置为false  
            "cwd": "${workspaceFolder}", // 调试程序时的工作目录，一般为${workspaceFolder}即代码所在目录  
            "environment": [],  
            "externalConsole": true, // 调试时是否显示控制台窗口，一般设置为true显示控制台  
            "MIMode": "gdb",  
            "miDebuggerPath": "D:\\MinGW64\\mingw64\\bin\\gdb.exe", // miDebugger的路径，注意这里要与MinGw的路径对应  
            "preLaunchTask": "g++", // 调试会话开始前执行的任务，一般为编译程序，c++为g++, c为gcc  
            "setupCommands": [  
                {   
		    "description": "Enable pretty-printing for gdb",  
                    "text": "-enable-pretty-printing",  
                    "ignoreFailures": true  
                }  
            ]  
        }  
    ]  
}
```
将`tasks.json`修改为
```java
{
    "version": "2.0.0",
    "command": "g++",
    "args": ["-g","${file}","-o","${fileBasenameNoExtension}.exe"],    // 编译命令参数
    "problemMatcher": {
        "owner": "cpp",
        "fileLocation": ["relative", "${workspaceFolder}"],
        "pattern": {
            "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",
            "file": 1,
            "line": 2,
            "column": 3,
            "severity": 4,
            "message": 5
        }
    }
}
```
## 最终实现`F5`调试

并且可以实现断点debug等功能

# 使用VSCode编辑博客

VSCode让我比较喜欢的一点是在于可以直接打开文件夹，并且继承了终端处理，git等，很多功能我还没有发掘出来，之后会深度体验一下VSCode！