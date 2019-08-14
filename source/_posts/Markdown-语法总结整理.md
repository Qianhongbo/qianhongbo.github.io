---
title: Markdown 语法总结整理
date: 2019-06-06 14:18:55
categories: 
- 学习笔记
tags: 
- Markdown
mathjax: true
---

# Markdown文本学习

就不总结了

直接上传一个我觉得不错的链接吧

[印象笔记Markdown文本入门指南](<https://list.yinxiang.com/markdown/eef42447-db3f-48ee-827b-1bb34c03eb83.php>)

# Markdown文本公式写法

[超全公式指南，简单易懂](https://blog.csdn.net/mingzhuo_126/article/details/82722455)

hhh ，比如：
$$
E=mc^2
$$

然而用这个写法在Hexo Next主题下网页上并不会显示出公式，我查了一下

[这个教程不错](<https://blog.csdn.net/yexiaohhjk/article/details/82526604>)

[打矩阵的时候你会发现无法多行显示，这个教程告诉了我们解决办法](https://jdhao.github.io/2017/10/06/hexo-markdown-latex-equation/)

# Markdown代码块的使用方法

```c++
#include1 <iostream>
```

在句首加"" ```" 即可，即键盘左上角

# Markdown引用的方法

> 引用内容

引用方法在句首‘ > ’加空格即可

# 修改文字的颜色

```html
浅红色文字：<font color="#dd0000">浅红色文字：</font><br /> 

深红色文字：<font color="#660000">深红色文字</font><br /> 

浅绿色文字：<font color="#00dd00">浅绿色文字</font><br /> 

深绿色文字：<font color="#006600">深绿色文字</font><br /> 

浅蓝色文字：<font color="#0000dd">浅蓝色文字</font><br /> 

深蓝色文字：<font color="#000066">深蓝色文字</font><br /> 

浅黄色文字：<font color="#dddd00">浅黄色文字</font><br /> 

深黄色文字：<font color="#666600">深黄色文字</font><br /> 

浅青色文字：<font color="#00dddd">浅青色文字</font><br /> 

深青色文字：<font color="#006666">深青色文字</font><br /> 

浅紫色文字：<font color="#dd00dd">浅紫色文字</font><br /> 

深紫色文字：<font color="#660066">深紫色文字</font><br /> 
```

效果如下：

浅红色文字：<font color="#dd0000">浅红色文字：</font><br /> 

深红色文字：<font color="#660000">深红色文字</font><br /> 

浅绿色文字：<font color="#00dd00">浅绿色文字</font><br /> 

深绿色文字：<font color="#006600">深绿色文字</font><br /> 

浅蓝色文字：<font color="#0000dd">浅蓝色文字</font><br /> 

深蓝色文字：<font color="#000066">深蓝色文字</font><br /> 

浅黄色文字：<font color="#dddd00">浅黄色文字</font><br /> 

深黄色文字：<font color="#666600">深黄色文字</font><br /> 

浅青色文字：<font color="#00dddd">浅青色文字</font><br /> 

深青色文字：<font color="#006666">深青色文字</font><br /> 

浅紫色文字：<font color="#dd00dd">浅紫色文字</font><br /> 

深紫色文字：<font color="#660066">深紫色文字</font><br /> 


# 修改字体大小

```html
size为1：<font size="1">size为1</font><br /> 
size为2：<font size="2">size为2</font><br /> 
size为3：<font size="3">size为3</font><br /> 
```
效果如下：

size为1：<font size="1">size为1</font><br /> 
size为2：<font size="2">size为2</font><br /> 
size为3：<font size="3">size为3</font><br /> 

# 修改背景色

```html
<table><tr><td bgcolor=#FF00FF>背景色的设置是按照十六进制颜色值：#7FFFD4</td></tr></table>
<table><tr><td bgcolor=#FF83FA>背景色的设置是按照十六进制颜色值：#FF83FA</td></tr></table>
<table><tr><td bgcolor=#D1EEEE>背景色的设置是按照十六进制颜色值：#D1EEEE</td></tr></table>
<table><tr><td bgcolor=#C0FF3E>背景色的设置是按照十六进制颜色值：#C0FF3E</td></tr></table>
```
<table><tr><td bgcolor=#FF00FF>背景色的设置是按照十六进制颜色值：#7FFFD4</td></tr></table>
<table><tr><td bgcolor=#FF83FA>背景色的设置是按照十六进制颜色值：#FF83FA</td></tr></table>
<table><tr><td bgcolor=#D1EEEE>背景色的设置是按照十六进制颜色值：#D1EEEE</td></tr></table>
<table><tr><td bgcolor=#C0FF3E>背景色的设置是按照十六进制颜色值：#C0FF3E</td></tr></table>

