---
title: About Python Decorator
date: 2017-07-26 23:07:28
tags: python
categories: python
---


`Decorator` 一个函数原有功能不变的情况下，增强这个函数的功能，这个就是装饰器。

case: when you difined a function , then you want to add some functions in it,but you don't want modify this function.


```python

#!/usr/bin/env python3
# -*- coding: utf-8 -*-

"""
This is a test decorator,it useful is going to print messages or bring function at a function's front and end.
"""
def at_front_end_print(fun):
    def byfun(*arg,**keyarg):
        print('At %s function\'s front print:' %(fun.__name__))
        fun(*arg,**keyarg)
        print('At %s function\'s end print:' %(fun.__name__))

    return byfun

"""
This is a test function,it test decorator at_front_end_print
"""
@at_front_end_print
def get_user_info(uid):
    print('This user id is %d :' %uid )

"""
This is a test function,it test decorator at_front_end_print
"""
@at_front_end_print
def check_user_bank_card(user , type=1):
    print('Do check %s user\'s status, for %d' %(user,type))

get_user_info(100)
check_user_bank_card(101,8)

"""
This follow Decorator may pass args for example: decorator's function do sign , like this it need three floors deep.
"""

def base_sign(sign_key):
    def decorator(fun):
        def byfun(*arg , **keyargs):
            print('This sector handle sign for %s :' % fun.__name__,'it\'s sign key is %s' %sign_key)
            fun(*arg , **keyargs)
        return byfun
    return decorator

"""
This is a open user api
"""

@base_sign(sign_key='abc')
def open_user(uid):
    print('This sector is handle user open %d:' %uid)

"""
This is a bank user api
"""
@base_sign('def')
def bank_user(uid):
    print('This sector is handle user bank %d:' %uid)

open_user(200)
bank_user(201)

```

<!-- more --> 

装饰器作用:

简化代码，避免`function`重复代码

打印日志：@log , 带参数@log('DEBUG') @log('INFO')

检测性能：@performance

数据库事务：@transaction

Url路由：@post('/register')