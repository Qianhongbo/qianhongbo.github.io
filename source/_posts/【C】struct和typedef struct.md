---
title: 【C】struct和typedef struct
date: 2019-08-14 17:42:08
categories: 
- 学习笔记
tags: 
- C
---
# 首先

注意在C和C++里不同

在C中定义一个结构体类型要用typedef:
```C
typedef struct Student
　　　　{
　　　　 int a;
　　　　}Stu;
```
于是在声明变量的时候就可：`Stu stu1` (如果没有typedef就必须用struct Student stu1;来声明)

这里的Stu实际上就是struct Student的别名。`Stu==struct Student`
　　　　
另外这里也可以不写Student（于是也不能struct Student stu1;了，必须是Stu stu1;）
```C
typedef struct
　　　 {
　　　　 int a;
　　　　}Stu;
```

但在c++里很简单，直接
```C++
struct Student
　　　　{
　　　　 int a;
　　　　};　　　　
```
于是就定义了结构体类型Student，声明变量时直接`Student stu2`

# 其次
　　　　
在c++中如果用typedef的话，又会造成区别：
```C++
struct Student
{
    int a;
}stu1;          //stu1是一个变量
```


```C++
typedef struct Student2
　　　　{
　　　　    int a;
　　　　}stu2;   //stu2是一个结构体类型=struct Student
```
使用时可以直接访问`stu1.a`，

但是stu2则必须先`stu2 s2`;然后`s2.a=10`;

# 最后

　typedef struct和struct的区别：

```C
typedef struct tagMyStruct
　　　　{ 
　　　　　int iNum;
　　　　　long lLength;
　　　　} MyStruct;

//上面的tagMyStruct是标识符，
//MyStruct是变量类型（相当于（int,char等））
```
这语句实际上完成两个操作：

1. 定义一个新的结构类型

```C
struct tagMyStruct
　　　　{　　 
　　　　　int iNum; 
　　　　　long lLength; 
　　　　};
```
2. typedef为这个新的结构起了一个名字，叫MyStruct

```C
　typedef struct tagMyStruct MyStruct;
```
```
typedef struct tagMyStruct
　　　　{ 
　　　　　int iNum;
　　　　　long lLength;
　　　　} MyStruct;
```

在C中，这个申明后申请结构变量的方法有两种：

- struct tagMyStruct 变量名

- MyStruct 变量名

在c++中可以有

- struct tagMyStruct 变量名

- MyStruct 变量名

- tagMyStruct 变量名