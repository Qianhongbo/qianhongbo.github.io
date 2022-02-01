---
title: Matlab机器人工具箱（Robotic Toolbox）仿真双六轴机械臂对侧打磨加工
date: 2020-05-10 21:36:49
categories: 
- 机器人学
tags: 
- Robotic Toolbox
---

本任务为完成大三下学期机器人学课程讨论是所做
实际上也是在之前做的双机仿真的基础上进行了修改。

最终结果如下：

[B站视频地址](https://www.bilibili.com/video/BV1t541147vs/)

<iframe src="//player.bilibili.com/player.html?aid=455552595&bvid=BV1t541147vs&cid=189565321&page=1" scrolling="no" border="0" frameborder="no" scrolling="no" width="95%" height="600"> </iframe>



> 使用的机器人工具箱版本为10.3.1版本
>
> 修改了工具箱自带的函数mdl_puma560，要使用代码需要在函数开头加上function p560 = mdl_puma560，结尾加上end以实现调用功能。

代码：

```matlab
%%
close all
clear;
d = 0.2;
plot3([0 0],[-1 1],[0 0])
p560_1 = mdl_puma560;
p560_1.name = 'puma1';
p560_1.base = [-0.8 0 0];
p560_2 = mdl_puma560;
p560_2.name = 'puma2';
p560_2.base = [0.8 0 0];
t=0:0.05:2;
%计算puma1机械臂移动到指定点，并将z轴指向朝前的齐次变换矩阵
T1 = transl(-0.4,0.2,0.1);
T2 = transl(0-d,-0.4,0)*troty(90);
%计算puma2机械臂移动到指定点，z轴指向朝后的齐次变换矩阵
T3 = transl(0.3,-0.3,0)*troty(-90);
T4 = transl(0+d,-0.4,0)*troty(-90);
%利用ctraj函数让两个机械臂运动到指定点
m1=ctraj(T1,T2,length(t)/2);
n1=ctraj(T3,T4,length(t)/2);
q1=p560_1.ikine6s(m1);
s1=p560_2.ikine6s(n1);
%移动到指定点后沿直线移动
T5 = transl(0-d,0.5,0)*troty(90);%puma1沿直线移动
T6 = transl(0+d,0.5,0)*troty(-90);%puma2沿直线移动                                                         
m2=ctraj(T2,T5,length(t));
n2=ctraj(T4,T6,length(t));
y1=p560_1.ikine6s(m2);
f1=p560_2.ikine6s(n2);
%重复
T7 = transl(0-d,0.5,0.2)*troty(90);
T8 = transl(0+d,0.5,0.2)*troty(-90); 
m3=ctraj(T5,T7,length(t)/2);
n3=ctraj(T6,T8,length(t)/2);
q2=p560_1.ikine6s(m3);
s2=p560_2.ikine6s(n3);
T9 = transl(0-d,-0.4,0.2)*troty(90);
T10 = transl(0+d,-0.4,0.2)*troty(-90);
m4=ctraj(T7,T9,length(t));
n4=ctraj(T8,T10,length(t));
y2=p560_1.ikine6s(m4);
f2=p560_2.ikine6s(n4);
%画图
p560_1.plot(q1);
hold on;
p560_2.plot(s1);
hold on;
p560_1.plot(y1);
hold on;
p560_2.plot(f1);
hold on;
plot3([0 0],[-1 1],[0.2 0.2])
p560_1.plot(q2);
hold on;
p560_2.plot(s2);
hold on;
p560_1.plot(y2);
hold on;
p560_2.plot(f2);
hold on;
```

由于Matlab无法实现双线程编程，所以并不是同步运动，所以考虑用循环的方式来实现“同步运动”，但本质上仍然不同步。

代码如下：

```matlab
%%
close all
clear;
d = 0.2;
plot3([0 0],[-1 1],[0 0])
p560_1 = mdl_puma560;
p560_1.name = 'puma1';
p560_1.base = [-0.8 0 0];
p560_2 = mdl_puma560;
p560_2.name = 'puma2';
p560_2.base = [0.8 0 0];
t=0:0.05:2;
%计算puma1机械臂移动到指定点，并将z轴指向朝下的齐次变换矩阵
T1 = transl(-0.4,0.2,0.1);
T2 = transl(0-d,-0.4,0)*troty(90);
%计算puma2机械臂移动到指定点，z轴指向朝上的齐次变换矩阵
T3 = transl(0.3,-0.3,0)*troty(-90);
T4 = transl(0+d,-0.4,0)*troty(-90);
%利用jtraj函数让两个机械臂运动到指定点
m1=ctraj(T1,T2,length(t)/2);
n1=ctraj(T3,T4,length(t)/2);
q1=p560_1.ikine6s(m1);
s1=p560_2.ikine6s(n1);
%移动到指定点后沿直线移动
T5 = transl(0-d,0.5,0)*troty(90);%puma1沿直线移动
T6 = transl(0+d,0.5,0)*troty(-90);%puma2沿直线移动                                                         
m2=ctraj(T2,T5,length(t));
n2=ctraj(T4,T6,length(t));
y1=p560_1.ikine6s(m2);
f1=p560_2.ikine6s(n2);
%重复
T7 = transl(0-d,0.5,0.2)*troty(90);
T8 = transl(0+d,0.5,0.2)*troty(-90); 
m3=ctraj(T5,T7,length(t)/2);
n3=ctraj(T6,T8,length(t)/2);
q2=p560_1.ikine6s(m3);
s2=p560_2.ikine6s(n3);
T9 = transl(0-d,-0.4,0.2)*troty(90);
T10 = transl(0+d,-0.4,0.2)*troty(-90);
m4=ctraj(T7,T9,length(t));
n4=ctraj(T8,T10,length(t));
y2=p560_1.ikine6s(m4);
f2=p560_2.ikine6s(n4);
%画图
for i=1:length(t)/2
p560_1.plot(q1(i,:));
hold on;
p560_2.plot(s1(i,:));
hold on;
end
for i=1:length(t)
p560_1.plot(y1(i,:));
hold on;
p560_2.plot(f1(i,:));
hold on
end
plot3([0 0],[-1 1],[0.2 0.2])
for i=1:length(t)/2
p560_1.plot(q2(i,:));
hold on;
p560_2.plot(s2(i,:));
hold on;
end
for i=1:length(t)
p560_1.plot(y2(i,:));
hold on;
p560_2.plot(f2(i,:));
hold on
end
```

