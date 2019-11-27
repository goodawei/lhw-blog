---
title: java－基础整数运算
date: 2017-07-23 23:11:03
tags: Java
categories: Java
---

### 整数运算

整数除法仍然是整数，`%`可以取余。

自增和自减运算：`++`和`--`，注意是先引用在自增，还是先自增在引用的差别。

简写的`+=`和`-=`

计算结果溢出不报错，但结果是错误的。 在计算过程中我们没有必要为了减少内存，采用byte,short等短类型进行计算。

### 移位运算

左移：`<<`

右移：`>>`

无条件右移：`>>>`

byte 和 short 会先转换为int在移位

注意移位如果超出了范围则直接被丢弃。For explame 5120 >> 11位

![java][1]

[1]: http://o8979n2hu.bkt.clouddn.com/java_%E7%A7%BB%E4%BD%8D.png

`int` 类型如上图在内存中占32位存储空间其中最高位是符号位。在右移过程中可以用`>>>`强制右移，将直接移动最高位的二进制数。

![java_right_move][2]

[2]: http://o8979n2hu.bkt.clouddn.com/java%20%E4%BD%8D%E8%BF%90%E7%AE%97%E7%A7%BB%E4%BD%8D.png

<!-- more -->

### 位运算

![java_wei_calut][3]

[3]: http://o8979n2hu.bkt.clouddn.com/java_%E4%BD%8D%E8%BF%90%E7%AE%97.png

位运算过程中会先按位对齐，然后一次按位进行与运算或者或运算For example:

![java_wei][4]

[4]: http://o8979n2hu.bkt.clouddn.com/java_%E4%BD%8D%E8%BF%90%E7%AE%97_%E4%BE%8B%E5%AD%90.png

### 异或运算

![java_yihuo][5]

[5]: http://o8979n2hu.bkt.clouddn.com/%E5%BC%82%E6%88%96%E8%BF%90%E7%AE%97.png

### 运算过程中类型的自动提升

Notice: 不能将较大的类型参与进入一个较小类型的运算当中。

![java_yihuo][6]

[6]: http://o8979n2hu.bkt.clouddn.com/java_%E7%B1%BB%E5%9E%8B%E8%87%AA%E5%8A%A8%E6%8F%90%E5%8D%87.png
