---
title: 【STM32】手把手写跑马灯实验
date: 2019-08-12 14:22:33
categories: 
- STM32
tags: 
- STM32
---

# 库函数版本

## 回顾GPIO基础知识

### 4种输入模式：

- 输入浮空
- 输入上拉
- 输入下拉
- 模拟输入

### 4种输出模式：

- 开漏输出
- 开漏复用功能
- 推挽式输出
- 推挽式复用功能

### 3种最大输出速度：

- 2MHZ
- 10MHz
- 50MHz

### 寄存器

- `GPIOx_CRL ` : 端口配置低寄存器（低8位）
- `GPIOx_CRH `: 端口配置高寄存器（高8位）
- `GPIOx_IDR  `: 端口输入寄存器
- `GPIOx_ODR  `: 端口输出寄存器
- `GPIOx_BSRR`: 端口位设置/清除寄存器
- `GPIOx_BRR` : 端口位清除寄存器
- `GPIOx_LCKR`: 端口配置锁存寄存器

## 跑马灯的硬件连接

- LED0连接PB5
- LED1连接PE5

## 库函数介绍

* 头文件：`stm32f10x_gpio.h`

* 源文件：`stm32f10x_gpio.c`

## 具体函数

### 1个初始化函数：

```C
void GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct);
//作用：初始化一个或者多个IO口（同一组）的工作方式和速度。该函数主要是操作GPIO_CRL(CRH)寄存器

//样例
 GPIO_InitTypeDef  GPIO_InitStructure;
	
 GPIO_InitStructure.GPIO_Pin = GPIO_Pin_5;         //LED0-->PB.5 端口配置
 GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;  //推挽输出
 GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; //IO口速度为50MHz
 GPIO_Init(GPIOB, &GPIO_InitStructure);	           //根据设定参数初始化GPIOB.5
```

### 2个读取输入电平函数：

```c
uint8_t GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
//作用：读取某个GPIO的输入电平。实际操作的是GPIOx_IDR寄存器。
//举例：
GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_5);//读取GPIOA.5的输入电平

uint16_t GPIO_ReadInputData(GPIO_TypeDef* GPIOx);
//作用：读取某组GPIO的输入电平。实际操作的是GPIOx_IDR寄存器。
//举例：
GPIO_ReadInputData(GPIOA);//读取GPIOA组中所有IO口输入电平
```

### 2个读取输出电平函数：

```c
uint8_t GPIO_ReadOutputDataBit (GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
//作用：读取某个GPIO的输出电平。实际操作的是GPIO_ODR寄存器。
//例如：
GPIO_ReadOutputDataBit(GPIOA, GPIO_Pin_5);//读取GPIOA.5的输出电平

uint16_t GPIO_ReadOutputData(GPIO_TypeDef* GPIOx);
//作用：读取某组GPIO的输出电平。实际操作的是GPIO_ODR寄存器。
//例如：
GPIO_ReadOutputData(GPIOA);//读取GPIOA组中所有io口输出电平
```

### 4个设置输出电平函数：

```c
void GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
//作用：设置某个IO口输出为高电平（1）。实际操作BSRR寄存器
//例如：
GPIO_SetBits(GPIOB,GPIO_Pin_5);   //GPIOB.5设置为高电平，LED高电平不亮

void GPIO_ResetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
//作用：设置某个IO口输出为低电平（0）。实际操作的BRR寄存器。
//例如：
 GPIO_ResetBits(GPIOB,GPIO_Pin_5);//GPIOB.5设置为低电平，LED高电平亮
```

## 代码思路

- 使能IO口时钟。调用函数`RCC_APB2PeriphColckCmd()`;
- 初始化IO口模式。调用函数`GPIO_Init()`;
- 操作IO口，输出高低电平`GPIO_SetBits()`,`GPIO_ResetBits()`;

## 最终代码

```c
//led.h 文件
#ifndef __LED_H
#define __LED_H

void LED_Init(void);

#endif 
```

```c
//led.c 文件
#include "led.h"
#include "stm32f10x.h"

void LED_Init(void)
{
	GPIO_InitTypeDef GPIO_InitStructure; //GPIO初始化定义
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE); //GPIOB时钟使能
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOE, ENABLE); //GPIOE时钟使能

	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;      //推挽输出
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_5;             //端口配置
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;     //IO口速度为50MHz
	GPIO_Init(GPIOB,&GPIO_InitStructure);                 //根据设定参数初始化GPIOB.5
	GPIO_SetBits(GPIOB,GPIO_Pin_5);                       //GPIOB.5设置为高电平，LED高电平不亮
	
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_5;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOE,&GPIO_InitStructure);
	GPIO_SetBits(GPIOE,GPIO_Pin_5);
}
```

```C
// main.c
#include "stm32f10x.h"
#include "led.h"
#include "delay.h"

int main(void)
{
		LED_Init();
		delay_init();
	
	while(1){
		GPIO_SetBits(GPIOB,GPIO_Pin_5);
		GPIO_SetBits(GPIOE,GPIO_Pin_5);
		delay_ms(500);
		
		GPIO_ResetBits(GPIOB,GPIO_Pin_5);
		GPIO_ResetBits(GPIOE,GPIO_Pin_5);
		delay_ms(500);
	}
}

```

# 寄存器版本

## 代码思路

- 使能IO口时钟。配置寄存器`RCC_APB2ENR`
- 初始化IO口模式。配置寄存器`GPIOx_CRH/CRL`
- 操作IO口，输出高低电平。配置寄存器`GPIOX_ODR`或者`BSRR/BRR`

## 最终代码

```c
//led.h 文件
//与库函数版本相同
#ifndef __LED_H
#define __LED_H

void LED_Init(void);

#endif 
```

```c
//led.c 文件
#include "led.h"
#include "stm32f10x.h"

void LED_Init(void)
{
	RCC->APB2ENR |= 1<<3;      //使能IO口时钟，GPIOB
	RCC->APB2ENR |= 1<<6;      //使能IO口时钟, GPIOE
	
	
	//GPIOB.5
	GPIOB->CRL &= 0xFF0FFFFF;  //先用与运算将欲修改的位修改为0（修改第六位）
	GPIOB->CRL |= 0x00300000;  //再用或运算将PB5修改为通用推挽输出，即0011，也就是3
	GPIOB->ODR |= 1<<5;        //将PB5的ODR寄存器设置为高电平，即最开始不亮
	
	//GPIOE.5
	GPIOE->CRL &= 0xFF0FFFFF;  //同理
	GPIOE->CRL |= 0x00300000;	
	GPIOE->ODR |= 1<<5;
	
}

```

```c
//main 文件
#include "stm32f10x.h"
#include "led.h"
#include "delay.h"

int main()
{
	LED_Init();
	delay_init();
	
	GPIOB->ODR |= 1<<5; 
	GPIOE->ODR |= 1<<5; 
	delay_ms(500);
	
	GPIOB->ODR = ~(1<<5);
	GPIOE->ODR = ~(1<<5);
	delay_ms(500);

}

```

# 位带操作版本

## 位操作原理

把每个比特膨胀为一个32位的字，当访问这些字的时候就达到了访问比特的目的，比如说BSRR寄存器有32个位，那么可以映射到32个地址上，我们去访问（读-改-写）这32个地址就达到访问32个比特的目的。

## 代码思路

- 使能IO口时钟。调用函数`RCC_APB2PeriphColckCmd()`
- 初始化IO口模式。调用函数`GPIO_Init()`
- 操作IO口，输出高低电平。**使用位带操作**。

## 最终代码

```c
//led.h 文件
#ifndef __LED_H
#define __LED_H
#define LED0 PBout(5)// PB5 添加宏定义
#define LED1 PEout(5)// PE5	

void LED_Init(void);

#endif 


```

```c
//main 文件
#include "stm32f10x.h"
#include "led.h"
#include "delay.h"

int main()
{
	LED_Init();
	delay_init();
	
	
	while(1){
		LED0 = 1;
		LED1 = 1;
		delay_ms(500);
		
		LED0 = 0;
		LED1 = 0;
		delay_ms(500);			
	}
}

```

