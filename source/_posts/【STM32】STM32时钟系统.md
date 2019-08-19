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
# SystemInit源代码
```C
void SystemInit (void)
{
  /* Reset the RCC clock configuration to the default reset state(for debug purpose) */
  /* Set HSION bit */
  RCC->CR |= (uint32_t)0x00000001;
  //或等于，也就是将最低位设置为1，其它位不变
  //将最低位设置为1，HSION：内部高速时钟使能

  /* Reset SW, HPRE, PPRE1, PPRE2, ADCPRE and MCO bits */
#ifndef STM32F10X_CL
  RCC->CFGR &= (uint32_t)0xF8FF0000;
#else
  RCC->CFGR &= (uint32_t)0xF0FF0000;
#endif /* STM32F10X_CL */   
  
  /* Reset HSEON, CSSON and PLLON bits */
  RCC->CR &= (uint32_t)0xFEF6FFFF;

  /* Reset HSEBYP bit */
  RCC->CR &= (uint32_t)0xFFFBFFFF;

  /* Reset PLLSRC, PLLXTPRE, PLLMUL and USBPRE/OTGFSPRE bits */
  RCC->CFGR &= (uint32_t)0xFF80FFFF;

#ifdef STM32F10X_CL
  /* Reset PLL2ON and PLL3ON bits */
  RCC->CR &= (uint32_t)0xEBFFFFFF;

  /* Disable all interrupts and clear pending bits  */
  RCC->CIR = 0x00FF0000;

  /* Reset CFGR2 register */
  RCC->CFGR2 = 0x00000000;
#elif defined (STM32F10X_LD_VL) || defined (STM32F10X_MD_VL) || (defined STM32F10X_HD_VL)
  /* Disable all interrupts and clear pending bits  */
  RCC->CIR = 0x009F0000;

  /* Reset CFGR2 register */
  RCC->CFGR2 = 0x00000000;      
#else
  /* Disable all interrupts and clear pending bits  */
  RCC->CIR = 0x009F0000;
#endif /* STM32F10X_CL */
    
#if defined (STM32F10X_HD) || (defined STM32F10X_XL) || (defined STM32F10X_HD_VL)
  #ifdef DATA_IN_ExtSRAM
    SystemInit_ExtMemCtl(); 
  #endif /* DATA_IN_ExtSRAM */
#endif 

  /* Configure the System clock frequency, HCLK, PCLK2 and PCLK1 prescalers */
  /* Configure the Flash Latency cycles and enable prefetch buffer */
  SetSysClock();

#ifdef VECT_TAB_SRAM
  SCB->VTOR = SRAM_BASE | VECT_TAB_OFFSET; /* Vector Table Relocation in Internal SRAM. */
#else
  SCB->VTOR = FLASH_BASE | VECT_TAB_OFFSET; /* Vector Table Relocation in Internal FLASH. */
#endif 
}
```
