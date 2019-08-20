---
title: 【STM32】端口复用
date: 2019-08-19 14:31:53
keywods: STM32,GPIO,串口
categories: 
- STM32
tags: 
- STM32
---
# 什么是端口复用

STM32有很多的内置外设，这些外设的外部引脚都是与GPIO复用的。也就是说，一个GPIO如果可以复用为内置外设的功能引脚，那么当这个GPIO作为内置外设使用的时候，就叫做复用。

>  例如串口1 的发送接收引脚是PA9,PA10，当我们把PA9,PA10不用作GPIO，而用做复用功能串口1的发送接收引脚的时候，叫端口复用。

# 端口复用配置过程
以PA9,PA10配置为串口1为例

- GPIO端口时钟使能。
```RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);```
- 复用外设时钟使能。比如你要将端口PA9,PA10复用为串口，所以要使能串口时钟。```RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE);```

- 端口模式配置。 GPIO_Init（）函数。查表：《STM32中文参考手册V10》P110的表格“8.1.11外设的GPIO配置”

![图片4](https://wx2.sinaimg.cn/large/006BuM4Jgy1g64z937e7zj30pb07qq4w.jpg)

```C
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);//①IO时钟使能

RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE);//②外设时钟使能

//③初始化IO为对应的模式
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9; //PA.9//复用推挽输出
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP; 
GPIO_Init(GPIOA, &GPIO_InitStructure);
  
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10;//PA10 PA.10 浮空输入
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;//浮空输入
GPIO_Init(GPIOA, &GPIO_InitStructure);  
```
