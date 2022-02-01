---
title: 【STM32】按键输入实验
date: 2019-08-13 14:07:34
keywods: STM32,GPIO,static 
categories: 
- STM32
tags: 
- STM32
---
# GPIO操作复习

- 读取IO口输入电平调用库函数为

```C
uint8_t GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
```

- 读取IO口输入电平操作寄存器为

```C
GPIOx_IDR:端口输入寄存器
```

- 使用位带操作读取IO口输入电平：

```C
 PEin(4)          //读取GPIOE.4口电平
 PEin(n)          //读取GPIOE.n口电平
```
# 步骤

- 使能按键对应IO口时钟。调用函数：`RCC_APB2PeriphClockCmd()`
- 初始化IO模式：上拉/下拉输入。调用函数：`GPIO_Init()`
- 扫描IO口电平（库函数/寄存器/位操作）

# 硬件连接

![key](http://wx3.sinaimg.cn/large/006BuM4Jgy1g607sfu2jyj30ku059q4q.jpg)

所以需要打开`PA`,`PE`的时钟，并打开`PA0`; `PE2,3,4`;

而且，对于KEY0，一端连接地，另一端连接PE4的IO口，KEY1，KEY2同理，所以当案件按下的时候，IO口坚持得到低电平。WK_UP正好相反。

# 按键扫描思路

## 扫描的两种类型
- 支持连续监测，比如遥控器选台的前进按钮，一直按会一直跳台
- 不支持连续监测，比如遥控器的关机/开机按钮，一直按但只会执行一次操作

## 引入C语言关键字：`static`

- Static申明的**局部变量**，存储在静态存储区。

- 它在函数调用结束之后，**不会被释放**。它的值会一直保留下来。

- 所以可以说static申明的局部变量，具有**记忆功能**。

```C
//利用两个函数说明问题
//1. 用int
int getValue(void)
{
  int  flag=0;
  flag++;
  return flag;
}

//2. 用static
int getValue(void)
{
  static int  flag=0; 
  /*初始化函数只会在第一次使用中运行，第二次调用getvalue函数就会直接跳到下面的语句，不会再次执行。*/
  flag++;
  return flag;
}
```
### 实际代码操作尝试！

#### 第一次

```C
/*demo1，将循环体放在两个getvalue中*/
#include <stdio.h>

int getvalue1(void){
    int a;
    for(int i = 10; i > 0; i--)
    {
        int a = 0;
        a++;
        return a;
    }
}

int getvalue2(void){
    for(int i = 10; i > 0; i--){
        static int b = 0;
        b++;
        return b;
    }
}

int main(){

    int c = getvalue1();
    int d = getvalue2();

    printf("%d, %d", c, d);
    getchar();
    return 0;
}
```

编译报`warning: control reaches end of non-void function`

编译结果为`“1,1”`，结果不对！

分析原因: 

- C语言必须要有返回值，例如return 0，或者return a等等
- 我将两个getvalue函数中的return写在了循环体里，并不会返回！
- 至于为什么出现了错误的结果，根据debug发现，并没有执行循环，而是只执行了一次，原因在于第一次就return了！

#### 第二次

进行如下修改：
```C
//将return修改到循环体之外
#include <stdio.h>

int getvalue1(void){
    for(int i = 10; i > 0; i--)
    {
        int a = 0;
        a++;      
    }
    return a;
    
}
int getvalue2(void){
    for(int i = 10; i > 0; i--)
    {
        static int b = 0;
        b++;
    }
    return b;
}

int main(){

    int c = getvalue1();
    int d = getvalue2();

    printf("%d, %d", c, d);
    getchar();
    return 0;
}
```


编译出现错误：
`identifier "a" is undefined；identifier "b" is undefined；`

原因：未定义变量a和b！

我的`int a`,`static int b`,都是在循环体里定义的，依然在函数中生效

#### 第三次

继续修改！
```C
#include <stdio.h>

int getvalue1(void){
    int a;                           //添加定义
    for(int i = 10; i > 0; i--)
    {
        int a = 0;
        a++;      
    }
    return a;
    
}
int getvalue2(void){
    int b;                           //添加定义
    for(int i = 10; i > 0; i--)
    {
        static int b = 0;
        b++;
    }
    return b;
}

int main(){

    int c = getvalue1();
    int d = getvalue2();

    printf("%d, %d", c, d);
    getchar();
    return 0;
}
```
编译未出现错误，但结果为`“0,1”`，结果不对

<font color="#dd0000">
分析原因：暂时未知，怀疑是循环体执行的`b`并没有并没有返回出来
</font><br />

#### 第四次

继续修改！
```C
//将循环体放在main中，而getvalue函数不执行循环功能
#include <stdio.h>

int getvalue1(void){
    int a = 0;
    a++;
    return a;
}

int getvalue2(void){
    static int b = 0;
    b++;
    return b;
}

int main(){
    int c,d;
    for(int i = 10; i >0; i--){
        int c = getvalue1();
        int d = getvalue2();
    }
    printf("%d, %d", c, d);
    getchar();
    return 0;
}
```
结果仍然不对...
得到的结果为：`7349584, 0`

原因重复定义？一个变量只能被定义一次！

一个函数外面对变量进行了定义，循环体里就不用定义了。static可能是个例外...

#### 第五次

最终修改版本：
```C
#include <stdio.h>

int getvalue1(void){
    int a = 0;
    a++;
    return a;
}

int getvalue2(void){
    static int b = 0;
    b++;
    return b;
}

int main(){
    int a,b;
    for(int i = 10; i >0; i--){
        a = getvalue1();
        b = getvalue2();
    }
    printf("%d, %d", a, b);
    getchar();
    return 0;
}
```
结果正确，得到`“1,10”`!!

#### 总结

问题在于C语言基础不牢，关于变量定义范围还需要进行复习！！

## 两种扫描的具体思路

```C
 //支持连续按
 //代码比较好理解
 u8 KEY_Scan(void)
    {
      if(KEY按下）
     {
           delay_ms(10)；//延时10-20ms，防抖。
           if(KEY确实按下)
            {
               return KEY_Value；
             }
          return 无效值；
     }
    }
```
```C
 //不支持连续按，按一次就是一次
 //代码稍显复杂，比较巧妙
 u8 KEY_Scan(void)
    {
     static u8 key_up=1;      //引入局部变量key_up
     if（key_up &&  KEY按下）
     {
        delay_ms(10);         //延时，防抖
        key_up=0;             //标记这次key已经按下
        if(KEY确实按下)
          {
           return KEY_VALUE;
          }
        }else if(KEY没有按下)  key_up=1;
       return 没有按下
    }

```
```C
//按键扫描二合一！
//函数需要给一个值
u8 KEY_Scan(u8 mode)
    {
     static u8 key_up=1;
     if(mode==1) key_up=1;//支持连续按
      if（key_up &&  KEY按下）
      {
        delay_ms(10);//延时，防抖
        key_up=0;//标记这次key已经按下
        if(KEY确实按下)
          {
           return KEY_VALUE;
          }
        }else if(KEY没有按下)  key_up=1;
       return 没有按下
    }
```

# 实际代码

```C
//key.h
#ifndef __KEY_H
#define __KEY_H	 

#define KEY0  GPIO_ReadInputDataBit(GPIOE,GPIO_Pin_4)  //读取按键0
#define KEY1  GPIO_ReadInputDataBit(GPIOE,GPIO_Pin_3)  //读取按键1
#define KEY2  GPIO_ReadInputDataBit(GPIOE,GPIO_Pin_2)  //读取按键2 
#define WK_UP   GPIO_ReadInputDataBit(GPIOA,GPIO_Pin_0)//读取按键3(WK_UP) 
#define KEY0_PRES 	1	//KEY0按下
#define KEY1_PRES	2	//KEY1按下
#define KEY2_PRES	3	//KEY2按下
#define WKUP_PRES   4	//KEY_UP按下(即WK_UP/KEY_UP)

void KEY_Init(void); //IO初始化
u8 KEY_Scan(u8);  	 //按键扫描函数


#endif
```
```C
//key.c
#include "stm32f10x.h"
#include "key.h"
#include "delay.h"

void KEY_Init(void)
{
	GPIO_InitTypeDef GPIO_InitStructure;
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA|RCC_APB2Periph_GPIOE,ENABLE); //使能PORTA,PORTE时钟
	GPIO_InitStructure.GPIO_Pin  = GPIO_Pin_2|GPIO_Pin_3|GPIO_Pin_4;          //KEY0-KEY2
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;                             //设置成上拉输入
	GPIO_Init(GPIOE, &GPIO_InitStructure);                                    //初始化GPIOE2,3,4
	//初始化 WK_UP-->GPIOA.0	  下拉输入
	GPIO_InitStructure.GPIO_Pin  = GPIO_Pin_0;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPD;                             //PA0设置成输入，默认下拉
	GPIO_Init(GPIOA, &GPIO_InitStructure);                                    //初始化GPIOA.0
}
//按键处理函数，用来返回按键值
//mode:0,不支持连续按;1,支持连续按;
//各种返回值代表的情况：
//0，没有任何按键按下
//1，KEY0按下
//2，KEY1按下
//3，KEY2按下 
//4，KEY3按下 WK_UP
u8 KEY_Scan(u8 mode)
{	 
	static u8 key_up=1;//按键按松开标志
	if(mode)key_up=1;  //支持连按		  
	if(key_up&&(KEY0==0||KEY1==0||KEY2==0||WK_UP==1)) // if条件句判读是否有按键按下，有则执行判断语句
	{
		delay_ms(10);    //去抖动 
		key_up=0;
		if(KEY0==0)return KEY0_PRES;
		else if(KEY1==0)return KEY1_PRES;
		else if(KEY2==0)return KEY2_PRES;
		else if(WK_UP==1)return WKUP_PRES;
	}else if(KEY0==1&&KEY1==1&&KEY2==1&&WK_UP==0)key_up=1; 	    
 	return 0;          // 无按键按下
}
```
```C
//main.c
#include "stm32f10x.h"
#include "led.h"
#include "beep.h"
#include "key.h"
#include "sys.h"
#include "delay.h"

int main()
{
	u8 key=0;	
	delay_init();	    	  //延时函数初始化	  
 	LED_Init();			      //LED端口初始化
	KEY_Init();           //初始化与按键连接的硬件接口
	beep_init();         	//初始化蜂鸣器端口
	LED0 = 0;					    //先点亮红灯
	while(1)
	{
 		key = KEY_Scan(0);	//得到键值
	   	if(key)
		{						   
			switch(key)
			{				 
				case WKUP_PRES:	//控制蜂鸣器
					BEEP = !BEEP; //取反，则蜂鸣器不叫
					break;
				case KEY2_PRES:	//控制LED0翻转
					LED0 = !LED0;
					break;
				case KEY1_PRES:	//控制LED1翻转	 
					LED1 = !LED1;
					break;
				case KEY0_PRES:	//同时控制LED0,LED1翻转 
					LED0 = !LED0;
					LED1 = !LED1;
					break;
			}
		}else delay_ms(10); 
	}	 
}
```
