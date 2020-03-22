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

在此附上机器人工具箱中关于各个参数option的定义，仅提供10版本

```matlab
switch opt.order
        case {'xyz', 'arm'}
            % XYZ order
            if numrows(roll) == 1
                R = rotx(yaw) * roty(pitch) * rotz(roll);
            else
                R = zeros(3,3,numrows(roll));
                for i=1:numrows(roll)
                    R(:,:,i) = rotx(yaw(i)) * roty(pitch(i)) * rotz(roll(i));
                end
            end
        case {'zyx', 'vehicle'}
            % ZYX order
            if numrows(roll) == 1
                R = rotz(yaw) * roty(pitch) * rotx(roll);
            else
                R = zeros(3,3,numrows(roll));
                for i=1:numrows(roll)
                    R(:,:,i) = rotz(yaw(i)) * roty(pitch(i)) * rotx(roll(i));
                end
            end
            
        case {'yxz', 'camera'}
            % YXZ order
            if numrows(roll) == 1
                R = roty(yaw) * rotx(pitch) * rotz(roll);
            else
                R = zeros(3,3,numrows(roll));
                for i=1:numrows(roll)
                    R(:,:,i) = roty(yaw(i)) * rotx(pitch(i)) * rotz(roll(i));
                end
            end
    end
end
```

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

## 建立连杆坐标系

```matlab
L(1)=Link('revolute','a',0,'alpha',0,'d',0);
L(1).mdh=1;
L(2)=Link('revolute','a',10,'alpha',pi/2,'d',0);
L(2).mdh=1;
L(3)=Link('revolute','a',6,'alpha',0,'d',0);
L(3).mdh=1;
R=SerialLink(L,'name','3RRR')
%改变一些参数
R.base=transl([-10 0 0]);
R.tool=transl([4 0 0]);
q0=[0 0 0];
R.plot(q0)
```

## 让连杆坐标系运动起来

```matlab
L(1)=Link('revolute','a',0,'alpha',0,'d',0);
L(1).mdh=1;
L(2)=Link('revolute','a',10,'alpha',pi/2,'d',0);
L(2).mdh=1;
L(3)=Link('revolute','a',6,'alpha',0,'d',0);
L(3).mdh=1;
R=SerialLink(L,'name','3RRR')
q1=linspace(0,pi/2,100)';
q2=linspace(0,pi/2,100)';
q3=linspace(0,pi/2,100)';
Q=[q1,q2,q3];
figure(2)
R.plot(Q)
R.fkine(Q(end,:))
```

# 实操任务

1. 建立两个机器人，机器人基座的相对位置可调。（实际环境中，机器人位置可能发生变动，需要进行修改）
2. 建立一个一条线段（一系列散点），线段的位置可调。（此线段即为机器人末端需要走的xyz轨迹点。实际环境中，轨迹位置随着工件安装位置而变换）
3. 针对线段上的任意一个点，使某一机器人末端以某种姿态到达该点。（为xyz轨迹点加上姿态，一般的，机械臂末端z轴应当和工件表面垂直。这里只有一条线段，可指定rpy为pi/4,0,0)
4. 针对线段上的任意一个点，使某一机器人末端以某种姿态，到达距离该点z向（末端坐标系下的z向）距离为某值的点。(此处表达的是待加工平板有一定厚度，而线段位于平板中部的情况。)
5. 在4的基础上，使得两个机器人末端z 向正对（xy不作要求），且两个机器人末端距离可调b

## Matlab仿真结果图

![19](https://tvax3.sinaimg.cn/large/006BuM4Jly1gd2tp522waj30ji0hgq42.jpg)

## 代码

```matlab
close all
clear;
d = 0.1;
plot3([0 0],[-1 1],[0 0])
p560_1 = mdl_puma560;
p560_1.name = 'puma1';
p560_1.base = [-0.6 0 0];
p560_2 = mdl_puma560;
p560_2.name = 'puma2';
p560_2.base = [0.6 0 0];
t=0:0.05:2;
%计算puma1机械臂移动到指定点，并将z轴指向朝下的齐次变换矩阵
T1 = transl(-0.4,0.2,0.1);
T2 = transl(0,-0.4,d)*trotx(180);
%计算puma2机械臂移动到指定点，z轴指向朝上的齐次变换矩阵
T3 = transl(0.2,-0.2,-0.1);
T4 = transl(0,-0.4,-d);
%利用jtraj函数让两个机械臂运动到指定点
q=p560_1.jtraj(T1,T2,t);
s=p560_2.jtraj(T3,T4,t);
%移动到指定点后沿直线移动
T5 = transl(0,0.5,d)*trotx(180);%puma1沿直线移动
T6 = transl(0,0.5,-d);%puma2沿直线移动                                                         
m=ctraj(T2,T5,50);
n=ctraj(T4,T6,length(t));
y=p560_1.ikine6s(m);
f=p560_2.ikine6s(n);
%画图
p560_1.plot(q);
hold on;
p560_2.plot(s);
hold on;
p560_1.plot(y);
hold on;
p560_2.plot(f);
hold on;
```