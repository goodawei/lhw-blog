---
title: Python基础数据类型
date: 2016-05-29 18:50:03
tags: python
categories: python
---

```python
#!/bin/python
#ecoding=utf-8

#倒入所需要的模块
import time
import os
import md5
import random

＃打印语句
print "你好!"

#变量的申明，前面不用加修饰类型
#--int类型
intA = 1;
intB = 2;
```
<!-- more -->
```python
#int类型和String 不能直接用+号，需要转成String再拼接。
print "values:" + str(intA + intB);

#boolean 类型 要注意里面的大小写 True和False
name = True

#if语句，需要一起和for注意的是后面加冒号（:）
if name== True:
    print "true"
else:
    print "false"
    
#for
for i in range(1,5,1):
    print i;

#方法（也在函数的叫法）的申明。同样不要忘记了冒号，因为Python不像其他语言那样还要括号，是按严格的格式来区分的所以个人也比较喜欢在方法后面加个结束注释。

def printName(name):
    print name
    print len(name)
    
#end fun printName

printName("my name is xxx")

#容器,习惯Java的朋友肯定也会像我一样想到数组（String[]）、集合（List、Map）Python也同样有类似的类型。

#列表List，类型Java中的集合List    
a = [1,2,3,"array"]
a.append("insert values")
a.insert(0, 0)
for v in a:
    print v
    
#元组 trupe，和Java中的数组类似  
array = ("array1","array2","array3")
for arr in array:
    print arr

#Dict 和Java中的map类似
mapValues = {"name":"text123","age":18}
print mapValues.get('age')
print mapValues['name']

#time函数，需要在头部添加 import time
time.sleep(1.5) #这里的单位是秒，和Java毫秒不一样。当然可以传入小数
print "print content"
print time.time() 
print time.strftime('%Y-%m-%d %H:%M:%d') # Java 中SimpleDateFormat

#os对系统一些操作
print os.name #使用的平台
print os.getcwd() #当前工作目录
files = os.listdir('E:\software')
#for f in files:
#print f
#os.remove('D://Test.java');

#MD5
m = md5.new("test123")
print m.digest()        #16位
print m.hexdigest()     #32位

#random
print random.random()

#file 
textFile = open("D:\\test.txt");
for line in textFile.readlines():
    print line
file.close()

```