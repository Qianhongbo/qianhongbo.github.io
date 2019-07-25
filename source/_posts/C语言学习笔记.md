---
title: c语言学习笔记
date: 2019-06-26 15:23:15
categories: 
- 学习笔记
tags: 
- C
---

## 我为什么要学习C语言

说来惭愧，作为一个工科学生，我竟然没有学过C语言，只在大一的时候学过一学期的VB，虽然语言这种东西说起来都差不多，但毕竟也是将近三年前的事了，早就忘光了。

可是不掌握C语言真的很麻烦，这尤其体现在做单片机上，单片机要求我们使用C语言，我当时连那些代码都看不懂，更别谈让我自己去写了。

正好，最近时间比较充沛。拿出时间来把C语言好好学学

p.s. 突然想起来我初中好像就学过C++了，但只是皮毛

## 我用什么工具学习C语言

书籍：C程序设计语言（第二版 新版）→ 纸质

软件：VS2017, Xcode

其它：Google，CSDN，Baidu

## 20190626

### 我的第一段代码

``` c
#include <stdio.h>
int main()
{
	printf("hello, world.\n");
	system("pause");
	//return 0;
}
```

1. main函数：main是一个特殊的函数名——每个程序都从main函数的起点开始执行。main函数通常会调用其它函数来帮助完成某些工作
2. 本例中main函数不需要任何参数
3. 用双引号括起来的字符序列称为字符串或字符串常量
4. \n 表示换行符

### 第二段代码

```c
#include <stdio.h>
#include <stdlib.h>
/* 当fatr = 0,20,...,300时，分别打印华氏温度与摄氏温度对照表 */
main()
{
	int fahr, celsius;
	int lower, upper, step;

	lower = 0; /* 温度表的上限 */
	upper = 300; /* 温度表的下限 */
	step = 20; /* 步长 */

	fahr = lower;
	while (fahr <= upper) {
		celsius = 5 * (fahr - 32) / 9;
		printf("%d\t%d\n", fahr, celsius);
		fahr = fahr + step;
	}
	system("pause");
}
```

1. 其中，int表示其后所列的变量为整数，与之对应的float表示浮点数（即可以带有小数部分）
2. 为什么不用5/9？因为C语言存在舍位，小数部分都会被舍弃