---
title: Matlab机器人工具箱（Robotic Toolbox）
date: 2020-03-10 20:50:12
categories: 
- 机器人学
tags: 
- Robotic Toolbox
---

Matlab机器人工具箱是我们在学习机器人学时候的一个非常好的工具，不但可以帮助我们计算，也可以做图形化的输出。

在此就不多谈安装问题，主要介绍各个函数的使用方法。

## 启动机器人工具箱

```matlab
startup_rvc;
```

## 绕xyz轴的旋转变化矩阵

```matlab
rotx(30*pi/180) %用弧度
rotx(30,'deg') %用角度
roty(30,'deg')
rotz(30,'deg')
```

## 绕xyz轴旋转的齐次变换矩阵

```matlab
trotx(30*pi/180) %用弧度
trotx(30,'deg') %用角度
troty(30,'deg')
trotz(30,'deg')
```

## 平移齐次变换矩阵

```matlab
transl([10,20,30])
```

## 旋转变换的连续乘积

```matlab
trotx(30,'deg')*troty(30,'deg')*trotz(30,'deg')
```

## rpy角

### 旋转矩阵

```matlab
roll=10*pi/180; pitch=20*pi/180; yaw=30*pi/180;
rpy2r(roll,pitch,yaw)
rpy2r(roll,pitch,yaw,'zyx')
rpy2r(roll,pitch,yaw,'xyz')
```

> 需要说名的是，rpy2r这个函数在不同的工具箱版本的函数并不一样，使用的角度顺序发生了改变，具体内容可以看具体版本的rpy2r.m文件。

### 检验

```matlab
rotx(yaw)*roty(pitch)*rotz(roll)
rotz(roll)*roty(pitch)*rotx(yaw)
rotx(roll)*roty(pitch)*rotz(yaw)
rotz(yaw)*roty(pitch)*rotx(roll)
```

### 结果

```matlab
%以下二者结果相同
rpy2r(roll,pitch,yaw,'zyx')
rotz(yaw)*roty(pitch)*rotx(roll)
%以下二者结果相同
rpy2r(roll,pitch,yaw,'xyz')
rotx(yaw)*roty(pitch)*rotz(roll)
```

> 个人认为没必要背下来，也没必要理解，只要想用的时候能知道它是怎么算的就好。

## Euler角

```matlab
%Euler角 旋转矩阵
alpha=10*pi/180; beta=20*pi/180; gama=30*pi/180;
eul2r(alpha,beta,gama)
%检验
rotz(alpha)*roty(beta)*rotz(gama)
```

## 齐次变换函数`homtrans`

```matlab
V=[30,40,60];T=trotx(30*pi/180);
V=[V';1];
homtrans(T,V)
%检验
T*V
```

## 绕任意轴旋转函数

```matlab
%绕任意轴旋转函数
theta=30*pi/180; k=[1;0;0];
angvec2r(theta,k)
```

