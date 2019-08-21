---
title: 【STM32】串口通信基本原理
date: 2019-08-19 16:56:38
keywods: STM32,串口
categories: 
- STM32
tags: 
- STM32
---
# 处理器与外部设备通信的两种方式
1. 并行通信
   - 传输原理：数据各个位同时传输。
   - 优点：速度快
   - 缺点：占用引脚资源多

2. 串行通信
   - 传输原理：数据按位顺序传输。
   - 优点：占用引脚资源少
   - 缺点：速度相对较慢

# 串行通信

## 分类

按照数据传送方向，分为：

1. 单工：数据传输只支持数据在**一个方向上**传输

2. 半双工：允许数据在两个方向上传输，但是，在某一时刻，只允许数据在一个方向上传输，它实际上是一种**切换方向**的单工通信；

3. 全双工：允许数据同时在两个方向上传输，因此，全双工通信是两个单工通信方式的结合，它要求发送设备和接收设备都有**独立的**接收和发送能力。 

## 串行通信的通信方式

1. 同步通信：**带时钟同步信号**传输。-SPI，IIC通信接口

2. 异步通信：**不带时钟同步信号**。-`UART`(通用异步收发器),单总线（必须约定波特率，按照相同的速度发送和读取）

# 常见的串行通信接口

![图片6](http://wx1.sinaimg.cn/large/006BuM4Jgy1g653m0aqzgj30ml0acdgt.jpg)

STM32的串口通信接口

- UART:通用异步收发器
- USART:通用同步异步收发器

大容量STM32F10x系列芯片，包含3个USART和2个UART

# UART异步通信方式引脚连接方法

- RXD:数据输入引脚。数据接受。
- TXD:数据发送引脚。数据发送。

![图片7](http://wx2.sinaimg.cn/large/006BuM4Jgy1g653v4l3u9j309g05hjrm.jpg)

![图片8](http://wx2.sinaimg.cn/large/006BuM4Jgy1g653vfp7wtj30d4059gm0.jpg)

![图片9](http://wx2.sinaimg.cn/large/006BuM4Jgy1g653wqh5f9j30j4071aa5.jpg)

# UART异步通信方式特点

- 全双工异步通信。
- 分数波特率发生器系统，提供精确的波特率。
- 发送和接受共用的可编程波特率，最高可达4.5Mbits/s
- 可编程的数据字长度（8位或者9位）；
- 可配置的停止位（支持1或者2位停止位）；
- 可配置的使用DMA多缓冲器通信。
- 单独的发送器和接收器使能位。
- 检测标志：① 接受缓冲器  ②发送缓冲器空 ③传输结束标志
- 多个带标志的中断源。触发中断。
- 其他：校验控制，四个错误检测标志。

# STM32串口异步通信需要定义的参数

 - 起始位，通信双方约定从空闲位开始
 - 数据位（8位或者9位），第九位就是奇偶校验位
 - 奇偶校验位（第9位），偶校验：使1的数量为偶数；奇校验：使1的数量为奇数
 - 停止位（1,15,2位）
 - 波特率设置
 
# 常用的串口相关寄存器

- USART_SR状态寄存器
- USART_DR数据寄存器
- USART_BRR波特率寄存器 

# 波特率计算方法

![图片11](http://wx1.sinaimg.cn/large/006BuM4Jgy1g654k6970sj30lv0d90z4.jpg)

# 串口操作相关库函数

```C
void USART_Init(); //串口初始化：波特率，数据字长，奇偶校验，硬件流控以及收发使能
void USART_Cmd();//使能串口
void USART_ITConfig();//使能相关中断

void USART_SendData();//发送数据到串口，DR
uint16_t USART_ReceiveData();//接受数据，从DR读取接受到的数据

FlagStatus USART_GetFlagStatus();//获取状态标志位
void USART_ClearFlag();//清除状态标志位
ITStatus USART_GetITStatus();//获取中断状态标志位
void USART_ClearITPendingBit();//清除中断状态标志位
```

# 串口配置的一般步骤

1. 串口时钟使能，GPIO时钟使能:`RCC_APB2PeriphClockCmd();`
2. 串口复位:`USART_DeInit()`; 这一步不是必须的
3. GPIO端口模式设置:`GPIO_Init();` 模式设置为`GPIO_Mode_AF_PP`
4. 串口参数初始化：`USART_Init();`
5. 开启中断并且初始化NVIC（如果需要开启中断才需要这个步骤）`NVIC_Init();``USART_ITConfig();`
6. 使能串口:`USART_Cmd();`
7. 编写中断处理函数：`USARTx_IRQHandler();`
8. 串口数据收发：
`void USART_SendData();//发送数据到串口，DR`, `uint16_t USART_ReceiveData();//接受数据，从DR读取接受到的数据`
9. 串口传输状态获取：
`FlagStatus USART_GetFlagStatus(USART_TypeDef* USARTx, uint16_t USART_FLAG);
void USART_ClearITPendingBit(USART_TypeDef* USARTx, uint16_t USART_IT);`
