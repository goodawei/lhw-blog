---
title: 用异或运算实现一种加密解密方法
date: 2016-05-29 19:31:42
tags: sign
categories: php
---

# 先来介绍PHP几个函数

```php
ctype_alpha    //检查字符串是否全是字母
ctype_lower    //检查字符串是否小写
ctype_upper    //检查字符串是否大写
ctype_digit    //检查字符串是否全是数字
```
<!-- more -->

```php
function StrCode($string, $action ='ENCODE') {
	$action!= 'ENCODE' && $string = base64_decode($string);
    $code= '';
	$key= 'abcdefghijklmnopqrstuvwxyz';
	$keyLen= strlen($key);
	$strLen= strlen($string);
	for($i = 0; $i < $strLen; $i++) {
		$k= $i % $keyLen;
		/*  1 ^ 1 (加密因子密钥) 0  ^ 1 = 1
		*  0 ^ 1 (加密因子密钥)  1  ^ 1 = 0 
		*  1 ^ 0 (加密因子密钥)  1  ^ 0 = 1
		*  0 ^ 0 (加密因子密钥)  0  ^ 0 = 0
		*/
		$code.= $string[$i] ^ $key[$k];
	}
	return($action != 'DECODE' ? base64_encode($code) : $code);
}
```