---
title: Java 变量和数据类型
date: 2017-04-10 20:58:51
tags: Java
categories: Java
---

### 变量 :stuck_out_tongue_winking_eye:

- 变量可以持有基本类型或指向某个对象
- 变量必须先定义后使用
- 定义变量
- 变量可以被赋值，还可以被重新赋值
- 等号`=`是赋值，不是数学的相等


### Eclipse Debug

进入调试模式后可以设置断点后单步执行

### 数据类型

- 整数类型：byte，short，int，long
- 浮点类型：float，double
- 字符类型：char
- 布尔类型：boolean

### 常量

- 用final定义
- 通常全部大写
- 避免magic number

### 计算机内存地址

如下图所示，每个内存正好存一个字节，算一下如果你有16G内存，大概有多少字节呢.

![计算机内存地址][1]

[1]: http://omduc9pze.bkt.clouddn.com/java%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B.png

java中,整型所对应内存的字节数如下图

![java内存地址][2]

[2]: http://omduc9pze.bkt.clouddn.com/java%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E5%AF%B9%E5%BA%94%E5%86%85%E5%AD%98.jpg

### 整型的范围

java 的整型是带符号的，注意要声明为long整型的时候要在后面加上L，否则是int类型，如下图

![java整型值范围][3]

[3]: http://omduc9pze.bkt.clouddn.com/java%E6%95%B4%E5%9E%8B.jpg

<!-- more -->

### 浮点数

```java
    float money = 100.12f // 结尾加f
    double d = 1.79e4
```

### 字符类型

注意字符类型和字符串类型的区别：字符类型用单引号，并且只有一个字符。

```java
    char c = 'a' ;
    char b = '李'；
```


### 二进制和十六进制表示整形的方式

```java
	int i3 = 2_000_000_000;
	int i4 = 0xff0000; // 十六进制表示的十进制16711680
	int i5 = 0b1000000000; // 二进制表示的十进制512
```

如果要输出一个以16进制表示的整型，或者以二进制表示的整型，可以使用JDK提供的方法：

```java

	// 输出以十六进制表示的整型的字符串：
	System.out.println(Integer.toHexString(12345678)); // bc614e
	// 输出以二进制表示的整型的字符串：
	System.out.println(Integer.toBinaryString(12345678)); // 101111000110000101001110

```

### 常量

不管PHP 还是 JAVA,许多可以避免硬编码的地方都可以用常亮去修饰，这样可以使我们更好地去理解程序的意图

```java
final double PI = 3.14
```
