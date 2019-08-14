---
title: 【STM32】按键输入实验
date: 2019-08-13 14:07:34
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
