---
title: How do you understand yield in php 
date: 2019-11-30 21:43:35
tags: 协程
---

yield 迭代器 协程  :zzz:

我们知道cpu是按照时间片段执行的程序的，php中当线程遇到yield的时候，可以让出cpu去执行另外一段程序，是表示协程最初始的一种形态。真正意义上的协程要复杂的多，比如通常会在影响性能的网络io环节实现协程处理，是应用程序在处理网络io的时候让出cpu去处理其他事情。

### 从系统内存等资源开销角度考虑其使用场景

大文件读取，一次性取出大的数据集 :seedling:

`yield` 的优势在于每次迭代只是到当前的`yield`就返回了，下次迭代在从它记录的位置在往下执行。不会消耗过多的系统资源。因为协程是程序之间上下文的切换，要比线程之间切换带来系统开销小。



<!-- more -->


```php
    public function nowExcute(Request $request)
    {
        foreach($this->fib() as $mobile){
            echo $mobile.PHP_EOL ;
        }
    }

    public function fib()
    {
        $handle = fopen('http://uc.dev/Jbh-JKFKLSJFKL4394.txt', "r");
        while (!feof ($handle))
        {
            $buffer  = fgets($handle);
            yield trim($buffer);
        }
        fclose ($handle);
    }
```

### 从算法设计角度考虑其使用场景

斐波那契数列的实现 :comet:

```php
	
    foreach($this->fib(10) as $n){
        echo  $n.PHP_EOL;
    }
    public function fib($num)
    {
        $n = 0 ; $a = 0 ; $b=1;
        while($n < $num){
            yield $b ;
            $t = $a ;
            $a = $b;
            $b = $b+$t;
            $n++;
        }
    }
```

```python

	def fin(num):
		n,a,b = 0,0,1
		while n<num:
			yield b      
			a,b = b,a+b
```

### 从程序功能设计角度考虑的使用场景

管道模式的实现

```php
(begin) ----------------> function() -----------------> (end)
            ^   ^   ^                   ^   ^   ^
            |   |   |                   |   |   |
            |   |   +------- M1() ------+   |   |
            |   +----------- ...  ----------+   |
            +--------------- Mn() --------------+

```

```php

function middleware($handlers,$arguments = []){
            //函数栈
            $stack = [];
            $result = null;
            foreach ($handlers as $handler) {
                // 每次循环之前重置，只能保存最后一个处理程序的返回值
                $result = null;
                $generator = call_user_func_array($handler, $arguments);
                if ($generator instanceof \Generator) {
                    //将协程函数入栈,为重入做准备
                    $stack[] = $generator;
                    //获取协程返回参数,到第一个yield地方返回，yield以下的程序在栈里取出在执行
                    $yieldValue = $generator->current();
                    //检查是否重入函数栈
                    if ($yieldValue === false) {
                        break;
                    }
                } elseif ($generator !== null) {
                    //不属于迭代器的function,获取function的返回值
                    $result = $generator;
                }
            }

            $return = ($result !== null);
            //将协程函数出栈
            while ($generator = array_pop($stack)) {
                if ($return) {
                    $generator->send($result);
                } else {
                    $generator->next();
                }
            }
        }

        $logMiddleware = function(){
            echo "日志中间件：进参记录日志 \n";
             yield;
            echo "日志中间件：出参记录日志 \n";
        };

        $validatorMiddleware = function (){
            echo "验证器中间件：前置验证器中间件 \n";
            $returnParms = yield;
            print_r($returnParms)."\n";
            echo "验证器中间件：后置验证器中间件 \n";
        };
        $handle = function (){
            return ['your English name'=>'Henry'];
        };

        middleware([$logMiddleware,$validatorMiddleware,$handle]);

```

输出如下：

```php

	日志中间件：进参记录日志
	验证器中间件：前置验证器中间件
	array('your English name'=>'Henry')
	验证器中间件：后置验证器中间件
	日志中间件：出参记录日志

```



