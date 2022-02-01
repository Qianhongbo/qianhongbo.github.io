---
title: 【STM32】外部中断实验
date: 2019-08-28 21:12:38
keywods: STM32,NVIC
categories: 
- STM32
tags: 
- STM32
---
# 外部中断概述

1. STM32的每个IO都可以作为外部中断输入。

2. STM32的中断控制器支持19个外部中断/事件请求：
- 线0~15：对应外部IO口的输入中断。
- 线16：连接到PVD输出。
- 线17：连接到RTC闹钟事件。
- 线18：连接到USB唤醒事件。

3. 每个外部中断线可以独立的配置触发方式（上升沿，下降沿或者双边沿触发），触发/屏蔽，专用的状态位。

4. 从上面可以看出，STM32供IO使用的中断线只有16个，但是STM32F10x系列的IO口多达上百个，那么中断线怎么跟io口对应呢？
- GPIOx.0映射到EXTI0
- GPIOx.1映射到EXTI1
- …
- GPIOx.15映射到EXTI15

5. IO口外部中断在中断向量表中只分配了7个中断向量，也就是
   只能使用7个中断服务函数
   >从表中可以看出，外部中断线5~9分配一个中断向量，共用一个服务函数，外部中断线10~15分配一个中断向量，共用一个中断服务函数。

# 外部中断常用库函数

```C
//exti.c文件
void GPIO_EXTILineConfig(uint8_t GPIO_PortSource, uint8_t GPIO_PinSource);
//设置IO口与中断线的映射关系

exp:  GPIO_EXTILineConfig(GPIO_PortSourceGPIOE,GPIO_PinSource2);

void EXTI_Init(EXTI_InitTypeDef* EXTI_InitStruct);
 //初始化中断线：触发方式等

ITStatus EXTI_GetITStatus(uint32_t EXTI_Line);
//判断中断线中断状态，是否发生

void EXTI_ClearITPendingBit(uint32_t EXTI_Line);
//清除中断线上的中断标志位
```
# 实际代码

```C
#include "exti.h"
#include "stm32f10x.h"
#include "key.h"
#include "delay.h"
#include "led.h"

void EXTIX_Init(void){
	
	EXTI_InitTypeDef  EXTI_InitStruct;
	NVIC_InitTypeDef  NVIC_InitStruct;
	
	KEY_Init();
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO,ENABLE);
	
	GPIO_EXTILineConfig(GPIO_PortSourceGPIOE,GPIO_PinSource4);
	
	EXTI_InitStruct.EXTI_Line = EXTI_Line4;
	EXTI_InitStruct.EXTI_LineCmd = ENABLE;
	EXTI_InitStruct.EXTI_Mode = EXTI_Mode_Interrupt;
	EXTI_InitStruct.EXTI_Trigger = EXTI_Trigger_Falling;
	EXTI_Init(&EXTI_InitStruct);
	
	NVIC_InitStruct.NVIC_IRQChannel = EXTI4_IRQn;
	NVIC_InitStruct.NVIC_IRQChannelCmd = ENABLE;
	NVIC_InitStruct.NVIC_IRQChannelPreemptionPriority = 2;
	NVIC_InitStruct.NVIC_IRQChannelSubPriority = 2;
	NVIC_Init(&NVIC_InitStruct);
}

void EXTI4_IRQHandler(void)
{
	delay_ms(10);
	if(KEY0 == 0){
		LED0 = !LED0;
		LED1 = !LED1;
	}
	//手动清除中断标志位
	EXTI_ClearITPendingBit(EXTI_Line4);
}
```

```C
//main.c文件
#include "stm32f10x.h"
#include "led.h"
#include "beep.h"
#include "key.h"
#include "sys.h"
#include "delay.h"
#include "exti.h"
#include "usart.h"

int main()
{
	delay_init();	    	  //延时函数初始化	  
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);
 	LED_Init();			      //LED端口初始化
	KEY_Init();           //初始化与按键连接的硬件接口
	EXTIX_Init();
	LED0 = 0;					    //先点亮红灯
	while(1)
	{
		printf("OK\r\n");
	}	 
}
```