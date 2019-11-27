---
title: About Python bit operation
date: 2017-07-26 23:17:38
tags: python
categories: python
---


```python

#!/usr/bin/env python3
# -*- coding: utf-8 -*-

"""
Python bit operation,when use bit operation case
"""

RECHARGE = 2>>1
WITHRAW  = 2<<0
INVENT   = 2<<1
BANDCARD = 2<<2

"""
If somebody will going to check recharge and bandcard,like this.
"""

this_user = RECHARGE | BANDCARD 

"""
How can check user like this.
"""

if this_user & RECHARGE:
    print('recharge is ok')
if this_user & WITHRAW:
    print('withraw is ok')
if this_user & BANDCARD:
    print('bandcard is ok')
if this_user & INVENT:
    print('invent is ok')
```