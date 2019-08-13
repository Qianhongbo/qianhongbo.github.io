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

# 使用VSCode编辑博客

VSCode让我比较喜欢的一点是在于可以直接打开文件夹，并且继承了终端处理，git等，很多功能我还没有发掘出来，之后会深度体验一下VSCode！