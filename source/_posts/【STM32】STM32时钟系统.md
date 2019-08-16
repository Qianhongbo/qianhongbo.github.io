---
title: 【STM32】STM32时钟系统和时钟系统初始化函数
date: 2019-08-15 15:49:16
keywods: STM32,RCC
categories: 
- STM32
tags: 
- STM32
photos: 'https://ws3.sinaimg.cn/large/006BuM4Jgy1g60eusarn7j30mw0f2gqp.jpg'
---
# 时钟系统知识总结

1. STM32 有5个时钟源:HSI、HSE、LSI、LSE、PLL。
- HSI是高速内部时钟，RC振荡器，频率为8MHz，精度不高。
- HSE是高速外部时钟，可接石英/陶瓷谐振器，或者接外部时钟源，频率范围为4MHz~16MHz。
- LSI是低速内部时钟，RC振荡器，频率为40kHz，提供低功耗时钟。WDG
- LSE是低速外部时钟，接频率为32.768kHz的石英晶体。RTC 
- PLL为锁相环倍频输出，其时钟输入源可选择为HSI/2、HSE或者HSE/2倍频可选择为2~16倍，但是其输出频率最大不得超过72MHz。

2. 系统时钟SYSCLK可来源于三个时钟源：       
- HSI振荡器时钟
- HSE振荡器时钟
- PLL时钟

3. STM32可以选择一个时钟信号输出到MCO脚(PA8)上，可以选择为PLL
  输出的2分频、HSI、HSE、或者系统时钟。

4. 任何一个外设在使用之前，必须首先使能其相应的时钟。

几个重要的时钟：

- SYSCLK(系统时钟) :
- AHB总线时钟
- APB1总线时钟(低速): 速度最高36MHz
- APB2总线时钟(高速): 速度最高72MHz
- PLL时钟

# RCC相关配置寄存器
```C
typedef struct
{
  __IO uint32_t CR;    //HSI,HSE,CSS,PLL等的使能和就绪标志位 
  __IO uint32_t CFGR;     //PLL等的时钟源选择，分频系数设定
  __IO uint32_t CIR;       // 清除/使能 时钟就绪中断
  __IO uint32_t APB2RSTR;  //APB2线上外设复位寄存器
  __IO uint32_t APB1RSTR;   //APB1线上外设复位寄存器
  __IO uint32_t AHBENR;    //DMA，SDIO等时钟使能
  __IO uint32_t APB2ENR;   //APB2线上外设时钟使能
  __IO uint32_t APB1ENR;   //APB1线上外设时钟使能
  __IO uint32_t BDCR;        //备份域控制寄存器
  __IO uint32_t CSR;           //控制状态寄存器
} RCC_TypeDef;
```
