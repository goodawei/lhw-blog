---
title: php相关新特性
date: 2016-06-25 21:52:10
tags: php
categories: php
---
# 数组相关

### 不定长参数的一种传递方式 php5.6以上

```php
	function add(...$args)
	{
		var_dump($args)
	}
		 
	//这里传入的参数个数是可以改变的
	add(1, 2, 3, 4);
		 
	//也可以传入数组，...会展开数组为多个参数
	$args = [1, 2, 3, 4];
	add(...$args);

```

以往实现方式：

```php
	function()
	{

		$args = func_get_args();
		$nums = func_num_args();
	}

```

<!-- more -->

### 多维数组的一种循环方式

```php
	$array = [
    	[1, 2],
    	[3, 4],
	];

	foreach ($array as list($a, $b)) {
    	echo "A: $a; B: $b\n";
	}
```

# trait的使用

### trait 使用场景和目的

`trait` 使用目的 ：

一 代码重用

二 螺丝钉作用，像别人的代码或者已完成的代码 插入新的代码


# `yield` 的使用

```php
	//迭代器，每次只返回一个元素，而不是创建个大数组一次返回
	//例如下面的读取文件，每次只返回一行，而不是将整个文件都一次性读入内存
	//使用迭代器可以很好的节约内存
	function getLines($file)
	{
    	$f = fopen($file, 'r');
    	try {
        	while ($line = fgets($f)) {
            	yield $line;
        	}
    	} finally {
        	fclose($f);
    	}
	}
	foreach (getLines("file.txt") as $n => $line) {
    	echo $line;
	}

```