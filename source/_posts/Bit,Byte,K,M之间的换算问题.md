---
title: Bit,Byte,K,M之间的换算问题
date: 2020-02-22 19:13:09
categories: 
- 知识积累
tags:
- 操作系统
---

Bit意为“位”或“比特”，是计算机运算的基础，属于二进制的范畴； 
Byte意为“字节”，是计算机文件大小的基本计算单位； 
换算： 

```
1 Byte = 8 Bits 

1 KB = 1024 Bytes 

1 MB = 1024 KB 

1 GB = 1024 MB 
```

Byte简写为B，而bit简写为b；1B=8b； 
一个数字与一个字母都是占1B；一个汉字占两个字节。

问题：计算机硬盘64G，每个块的大小为4K，用位示图来管理硬盘的空间，则位示图的大小为多少字节？

- [ ] 16M
- [ ] 4M
- [x] 2M
- [ ] 1M

解答：
$$
\frac{64G}{4K}=\frac{64\times1024\times1024K}{4K}次(bit)=\frac{16\times1024\times1024}{8\times1024\times1024}=2M
$$
