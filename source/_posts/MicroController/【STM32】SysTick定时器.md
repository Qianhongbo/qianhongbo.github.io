---
title: 【STM32】SysTick定时器
date: 2019-08-19 10:33:47
keywods: STM32,SysTick
categories: 
- STM32
tags: 
- STM32
---
# Systick定时器是什么
- Systick定时器，是一个简单的定时器，对于CM3,CM4内核芯片，都有Systick定时器。

- Systick定时器常用来做延时，或者实时系统的心跳时钟。这样可以节省MCU资源，不用浪费一个定时器。比如UCOS中，分时复用，需要一个最小的时间戳，一般在STM32+UCOS系统中，都采用Systick做UCOS心跳时钟。

- Systick定时器就是系统滴答定时器，一个24 位的倒计数定时器，计到0 时，将从RELOAD 寄存器中自动重装载定时初值。只要不把它在SysTick 控制及状态寄存器中的使能位清除，就永不停息，即使在睡眠模式下也能工作。

- SysTick定时器被捆绑在NVIC中，用于产生SYSTICK异常（异常号：15）

- Systick中断的优先级也可以设置。

# Systick相关寄存器

- CTRL             SysTick 控制和状态寄存器  LOAD 
![图片2](https://wx4.sinaimg.cn/large/006BuM4Jgy1g64su5lcdnj30oo02ngm8.jpg)

- SysTick       自动重装载除值寄存器
![图片1](https://wx3.sinaimg.cn/large/006BuM4Jgy1g64sme60v6j30oh07wad1.jpg)
对于STM32，外部时钟源是 HCLK(AHB总线时钟）的1/8内核时钟是 HCLK时钟,配置函数：SysTick_CLKSourceConfig()
    
- VAL                SysTick 当前值寄存器  
![图片3](https://wx3.sinaimg.cn/large/006BuM4Jgy1g64svteyv1j30od04aq4c.jpg)

- CALIB            SysTick 校准值寄存器

# Systick库函数
 
固件库中的Systick相关函数：

```C
SysTick_CLKSourceConfig()    //Systick时钟源选择  misc.c文件中
SysTick_Config(uint32_t ticks) 
//初始化systick,时钟为HCLK,并开启中断
//core_cm3.h/core_cm4.h文件中

//Systick中断服务函数：
void SysTick_Handler(void);
```
```C
//SysTick_CLKSourceConfig函数
void SysTick_CLKSourceConfig(uint32_t SysTick_CLKSource)
{
  /* Check the parameters */
  assert_param(IS_SYSTICK_CLK_SOURCE(SysTick_CLKSource));

  if (SysTick_CLKSource == SysTick_CLKSource_HCLK)
  {
    SysTick->CTRL |= SysTick_CLKSource_HCLK;
  }
  else
  {
    SysTick->CTRL &= SysTick_CLKSource_HCLK_Div8;
  }
}
```
```C
//SysTick_CLKSourceConfig函数
static __INLINE uint32_t SysTick_Config(uint32_t ticks)
{ 
  if (ticks > SysTick_LOAD_RELOAD_Msk)  return (1);         /* Reload value impossible */
   
 /* set reload register */                                                            
  SysTick->LOAD  = (ticks & SysTick_LOAD_RELOAD_Msk) - 1; 
/* set Priority for Cortex-M0 System Interrupts */
  NVIC_SetPriority (SysTick_IRQn, (1<<__NVIC_PRIO_BITS) - 1); 
  SysTick->VAL   = 0;                                        /* Load the SysTick Counter Value */
  SysTick->CTRL  = SysTick_CTRL_CLKSOURCE_Msk | 
                   SysTick_CTRL_TICKINT_Msk   | 
                   SysTick_CTRL_ENABLE_Msk;     /* Enable SysTick IRQ and SysTick Timer */
  return (0);                                                  /* Function successful */
}
```
```C
//用中断的方式实现delay延时
static __IO uint32_t TimingDelay;
void Delay(__IO uint32_t nTime)
{ 
   TimingDelay = nTime;
   while(TimingDelay != 0);
}
void SysTick_Handler(void)
{
    if (TimingDelay != 0x00) 
     { 
       TimingDelay--;
     }
}
 int main(void)
 {  …
    if (SysTick_Config(SystemCoreClock / 1000)) //systick时钟为HCLK，中断时间间隔1ms
     {
     while (1);
     }
    while(1)
     { Delay(200);//2ms
     … 
     }
}
```
```C
void delay_init()
{
#if SYSTEM_SUPPORT_OS  							//如果需要支持OS.
	u32 reload;
#endif
	SysTick_CLKSourceConfig(SysTick_CLKSource_HCLK_Div8);	//选择外部时钟  HCLK/8
	fac_us=SystemCoreClock/8000000;				//为系统时钟的1/8  
#if SYSTEM_SUPPORT_OS  							//如果需要支持OS.
	reload=SystemCoreClock/8000000;				//每秒钟的计数次数 单位为K	   
	reload*=1000000/delay_ostickspersec;		//根据delay_ostickspersec设定溢出时间
												//reload为24位寄存器,最大值:16777216,在72M下,约合1.86s左右	
	fac_ms=1000/delay_ostickspersec;			//代表OS可以延时的最少单位	   

	SysTick->CTRL|=SysTick_CTRL_TICKINT_Msk;   	//开启SYSTICK中断
	SysTick->LOAD=reload; 						//每1/delay_ostickspersec秒中断一次	
	SysTick->CTRL|=SysTick_CTRL_ENABLE_Msk;   	//开启SYSTICK    

#else
	fac_ms=(u16)fac_us*1000;					//非OS下,代表每个ms需要的systick时钟数   
#endif
}								    

#if SYSTEM_SUPPORT_OS  							//如果需要支持OS.
//延时nus
//nus为要延时的us数.		    								   
void delay_us(u32 nus)
{		
	u32 ticks;
	u32 told,tnow,tcnt=0;
	u32 reload=SysTick->LOAD;					//LOAD的值	    	 
	ticks=nus*fac_us; 							//需要的节拍数	  		 
	tcnt=0;
	delay_osschedlock();						//阻止OS调度，防止打断us延时
	told=SysTick->VAL;        					//刚进入时的计数器值
	while(1)
	{
		tnow=SysTick->VAL;	
		if(tnow!=told)
		{	    
			if(tnow<told)tcnt+=told-tnow;		//这里注意一下SYSTICK是一个递减的计数器就可以了.
			else tcnt+=reload-tnow+told;	    
			told=tnow;
			if(tcnt>=ticks)break;				//时间超过/等于要延迟的时间,则退出.
		}  
	};
	delay_osschedunlock();						//恢复OS调度									    
}
//延时nms
//nms:要延时的ms数
void delay_ms(u16 nms)
{	
	if(delay_osrunning&&delay_osintnesting==0)	//如果OS已经在跑了,并且不是在中断里面(中断里面不能任务调度)	    
	{		 
		if(nms>=fac_ms)							//延时的时间大于OS的最少时间周期 
		{ 
   			delay_ostimedly(nms/fac_ms);		//OS延时
		}
		nms%=fac_ms;							//OS已经无法提供这么小的延时了,采用普通方式延时    
	}
	delay_us((u32)(nms*1000));					//普通方式延时  
}
#else //不用OS时
//延时nus
//nus为要延时的us数.		    								   
void delay_us(u32 nus)
{		
	u32 temp;	    	 
	SysTick->LOAD=nus*fac_us; 					//时间加载	  		 
	SysTick->VAL=0x00;        					//清空计数器
	SysTick->CTRL|=SysTick_CTRL_ENABLE_Msk ;	//开始倒数	  
	do
	{
		temp=SysTick->CTRL;
	}while((temp&0x01)&&!(temp&(1<<16)));		//等待时间到达   
	SysTick->CTRL&=~SysTick_CTRL_ENABLE_Msk;	//关闭计数器
	SysTick->VAL =0X00;      					 //清空计数器	 
}
//延时nms
//注意nms的范围
//SysTick->LOAD为24位寄存器,所以,最大延时为:
//nms<=0xffffff*8*1000/SYSCLK
//SYSCLK单位为Hz,nms单位为ms
//对72M条件下,nms<=1864 
void delay_ms(u16 nms)
{	 		  	  
	u32 temp;		   
	SysTick->LOAD=(u32)nms*fac_ms;				//时间加载(SysTick->LOAD为24bit)
	SysTick->VAL =0x00;							//清空计数器
	SysTick->CTRL|=SysTick_CTRL_ENABLE_Msk ;	//开始倒数  
	do
	{
		temp=SysTick->CTRL;
	}while((temp&0x01)&&!(temp&(1<<16)));		//等待时间到达   
	SysTick->CTRL&=~SysTick_CTRL_ENABLE_Msk;	//关闭计数器
	SysTick->VAL =0X00;       					//清空计数器	  	    
} 
#endif 
```
