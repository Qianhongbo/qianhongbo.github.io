---
title: 【从零入门Qt】开发一款属于自己的串口调试助手
date: 2019-07-31 17:22:58
categories: 
- 学习笔记
tags:
- Qt
---

暑假来深圳这边实习，做的是嵌入式方向，负责人分配给我的第一个任务是用Qt写一个串口调试助手。这个任务总体来讲难度适中，尽管我没接触过Qt，但能查到很多很多的资料，甚至包括别人写好的源代码，所以有很多的学习资源。

我第一天在同学的推荐下，看了一个入门的文档，[《Qt 学习之路 2》](https://www.devbean.net/2012/08/qt-study-road-2-catelog/)，其中内容讲的比较基础，基本可以理解，算是能对Qt的语法了解个大概。而且如果实在不懂，也可以参看b站上的视频教程，是[黑马程序员的教程](https://www.bilibili.com/video/av20446734)，视频内容比较冗长，但对于初学者来说可能更好理解，各取所需，看个人选择。

因为负责人只给了我三天的时间，所以我也就花了一天左右的时间看教程，就开始看我在网上看的一份源码了。当时我的进度是黑马程序员看了不到10个视频，《Qt 学习之路 2》看了不到20节教程，虽然说看了不少，但我当时还是很慌的，对于我这种小白来说，还是不知道源代码是什么意思，但这个时候我能大概明白每个函数要做什么功能了，也就是我看懂了函数名，但函数里的句子我看不懂。

然后我就选择了按照这份代码自己复刻一份，算是尝试着自己写一下。

于是我新建了一个`project`，名字是`Myserialport`，发现源代码中`main.cpp`完全一样，也就是一个窗体的显示功能，很好理解，直接略去~

上面的`mainwindow.h`文件里面有很多库函数的引用，函数的声明，我姑且认为不重要，暂时略去

而代码最多的就集中在`mainwindow.cpp`文件里了，也是我最需要解决的部分！

这是我新建工程后的代码：

```c++
#include "mainwindow.h"
#include "ui_mainwindow.h"

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
}

MainWindow::~MainWindow()
{
    delete ui;
}

```

而源代码在`MainWindow::MainWindow(QWidget *parent)`中添加了很多窗体生成的初始化语句

```c++
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    textstate_receive = false;                 //初始化发送窗口状态为文本模式
    textstate_send = false;                    //初始化接收窗口状态为文本模式
    serialport = new QSerialPort;              //创建QSerialPort类，需要在头文件中添加#include <QSerialPort>
    ui->send_button->setEnabled(false);        //初始化接收按钮不可用
    ui->close_port->setEnabled(false);         //初始化接收按钮不可用
    ui->Receive_text_window->setReadOnly(true);//讲接收窗口设置为只读窗口
    setWindowTitle("串口调试助手");              //设置程序的标题
    setWindowIcon(QIcon(":/images/exe.jpg"));  //设置程序左上角图标
    find_port();                               //查找可用串口函数
}
```

以上语句还是比较好理解的，每句话我都进行了注释。

下面的代码就是定义各个函数，而.h文件里的很多函数声明也就很好理解了，也就是在.h文件里定义一下各个函数

比如以下很重要的两个函数，用于实现数据的发送和接收

```c++
//发送数据
void MainWindow::on_send_button_clicked()
{
    if(textstate_send == false)  //文本模式
    {
        serialport->write(ui->Send_text_window->toPlainText().toUtf8());
    }

    if(textstate_send == true)     //hex模式
    {
        QString str = ui->Send_text_window->toPlainText();
        int num = str.toInt();
        str = str.setNum(num,16);
        ui->Send_text_window->clear();
        ui->Send_text_window->append(str);
        serialport->write(ui->Send_text_window->toPlainText().toLatin1());
    }
}
```

```c++
//窗口显示串口传来的数据
void MainWindow::Read_Data()
{
    QByteArray buf;
    buf = serialport->readAll();
    if(!buf.isEmpty())          //将数据显示到文本串口
    {

        if(textstate_receive == false)   //文本模式
        {
            QString str = ui->Receive_text_window->toPlainText();
            str += tr(buf);
            ui->Receive_text_window->clear();
            ui->Receive_text_window->append(str);
        }
        if(textstate_receive == true)   //hex模式
        {
            QString str = ui->Receive_text_window->toPlainText();
            // byteArray 转 16进制
            QByteArray temp = buf.toHex().toUpper();
            str += tr(temp);
            ui->Receive_text_window->clear();
            ui->Receive_text_window->append(str);
        }
    }
    buf.clear();    //清空缓存区
}
```

最后实现了以下的界面，以及文本模式和16进制（hex模式）两种模式转换的数据收发

p.s.：尝试了很久想要解决中文无法显示的问题，最终无果...



[有需要的话可以在这个链接下载嗷~](https://download.csdn.net/download/weixin_42559298/11457836)

