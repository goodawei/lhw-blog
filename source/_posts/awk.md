---
title: awk
date: 2019-11-29 21:58:51
tags: Linux
---


Reference：`https://www.gnu.org/software/gawk/manual/gawk.html#Running-gawk`

```bash
Amelia       555-5553     amelia.zodiacusque@gmail.com    F
Anthony      555-3412     anthony.asserturo@hotmail.com   A
Becky        555-7685     becky.algebrarum@gmail.com      A
Bill         555-1675     bill.drowning@hotmail.com       A
Broderick    555-0542     broderick.aliquotiens@yahoo.com R
Camilla      555-2912     camilla.infusarum@skynet.be     R
Fabius       555-1234     fabius.undevicesimus@ucb.edu    F
Julie        555-6699     julie.perscrutabor@skeeve.com   F
Martin       555-6480     martin.codicibus@hotmail.com    A
Samuel       555-3430     samuel.lanceolis@shu.edu        A
Jean-Paul    555-2127     jeanpaul.campanorum@nyu.edu     R
```

### 按 $0 查找匹配元素

```bash
awk '/li/ {print $0}' 2017.log 
<=>
grep li 2017.log

//不同的是 ‘/’ is called a regular expression,
```

<!-- more -->

### 切割 slice

`uuidd:x:107:111::/run/uuidd:/bin/false`

如果每行串都有某个特殊字符可以按照特殊字符切割字符串。

```bash
awk -F ':' ' /redis/ { print $1 }' /etc/passwd

awk -F ':' '/redis/ { print $1 }' /etc/passwd  | wc -l

awk -F ':' ' { print $1 }' /etc/passwd | sort 
```

### COUNT THIS FILE 

统计文件有多少列
`awk 'END { print NF }' 2017.log `
统计文件有多少行
`awk 'END { print NR }' 2017.log`