---
title: 【C】从GPIO_Init引发的关于C的研究
date: 2019-08-14 16:26:18
categories: 
- 学习笔记
tags: 
- C
- STM32
---
在我看下面这段代码对`GPIO_Init`有一些不理解

```C
void BEEP_Init(void)
{
 GPIO_InitTypeDef  GPIO_InitStructure;     //声明变量
 	
 RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
 GPIO_InitStructure.GPIO_Pin = GPIO_Pin_8;				 
 GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; 		 
 GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;	 
 
 GPIO_Init(GPIOB, &GPIO_InitStructure);	  //不理解
 GPIO_ResetBits(GPIOB,GPIO_Pin_8);
}
```


首先看一下`GPIO_Init`的语法

```C
void GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct);
```
我产生了几个疑问：

1. `GPIO_TypeDef* GPIOx`是什么意思？
2. 为什么`&GPIO_InitStructure`要用指针类型？
3. 为什么`GPIOB`不用使用指针类型？

```C
//这里定义了一个结构体
typedef struct
{
  u16 GPIO_Pin;
  GPIOSpeed_TypeDef GPIO_Speed;
  GPIOMode_TypeDef GPIO_Mode;
}GPIO_InitTypeDef;     //代码里用它来声明变量
```
>怎么理解呢？  
int 是 类型， i 是变量  
GPIO_InitTypeDef GPIO_InitStructure;  
同样，GPIO_InitTypeDef 是类型，GPIO_InitStructure 是变量！

>C语言里面没有这种GPIO_InitTypeDef 类型 ，所以要自己 通过 typedef 来定义这种类型。

引用自一个知乎问题，[链接](https://www.zhihu.com/question/21136956)

>一般int* p表示p类型是一个int型指针，int *p表示p是一个指针，指向的类型是int。

也就是`GPIO_TypeDef* GPIOx`等同于`GPIO_TypeDef *GPIOx`，而且我之前对于`init *p`也有误解，我以为这是在声明一个`init`常量，我当时误认`*`已经把地址取值了，实则是造成了误会，它是定义了一个指向`init`类型的指针变量，也就是`p`，所以从这点考虑，`init* p`或许表意更清楚一点！

终于搞懂了！！！

第二个问题也就好解释了，因为`GPIO_Init`就是要求输入两个指针变量

第三个问题

> GPIOB本身就是一个指针类型！
