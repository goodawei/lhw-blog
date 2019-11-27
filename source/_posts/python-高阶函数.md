---
title: Python 高阶函数
date: 2017-07-24 22:04:09
tags: python
categories: python
---

###  reference:  http://book.pythontips.com/en/latest/


Let's going to understand Python's map , reduce , filter.

<!-- more --> 

```python

#!/usr/bin/env python3
# -*- coding: utf-8 -*-

from functools import reduce

# use input,like Php readline ext
input = input("please input:")

print(list(map(int , input.split(','))))

str = 'dd,wer,   '

print(str.lstrip('d'))

# use Map

fruitList = ["apple","orange","banana"]

# this map returns is  an iterable object. so i don't understand how can i retrieve or take out i want elements
iterter = map(lambda item: item+"s" , fruitList)
for value in iterter:
    print(value)


# list is may any data type even object or function for example

# if you invest 10000 yuan and you need to know each manth's replay plan,then i hope define 12 manth handle to give map lists
def getJan(money):
    return 'this is Jan\'s repaly plan'

def getFeb(money):
    return 'this is Feb\'s replay plan'

def getMar(money):
    return 'this is Mar\'s replay plan'

money = 10000
rep = map(lambda getRepaly : getRepaly(money)  , [getJan,getFeb,getMar])
for value in rep:
    print(value)


#filter : as the name suggests , it return a list elements for which a function returns true.

prepareFilter =  ['php','java','python'];
for value in filter(lambda language: language != 'php' , prepareFilter):
    print(value)

# reduce It applies a rolling computation to sequential pairs of values in a list.
# Notice : If you want to use reduce need  to from functools import reduce

points = [1,2,3,4,5];
print(reduce(lambda x ,y: x+y ,points))

```




