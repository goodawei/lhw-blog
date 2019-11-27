---
title: Python AES and PHP AES
date: 2017-07-24 21:53:25
tags: python
categories: python
---


When i try use Python's package of json.dumps,I find the result is difference PHP, There is a question about Python's json.dumps. It can behand `:` add a place.(它会在 ‘:’ 后放置一个空格，而像PHP这样的语言是没有这样的hash.这样导致两种语言的AES结果不一致).

<!-- more -->

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

from Crypto.Cipher import AES
import json
import base64
import hashlib

key= 'LJl:*DOOUV8({t11';
iv = 'OK~aAEHi{yxfXDf4';

#这里密钥key 长度必须为16（AES-128）,
#24（AES-192）,或者32 （AES-256）Bytes 长度
#目前AES-128 足够目前使用
BS = 16

#pad = lambda s: s + (BS - len(s) % BS) * '\0'
pad = lambda s: s + (BS - len(s) % BS) * chr(0)

Aesdata = {'uid':'361503611'}

#text = json.dumps(Aesdata, sort_keys=True, indent=4, separators=(',', ':'))
text = json.dumps(Aesdata,separators=(',', ':'))

cryptor = AES.new(key,AES.MODE_CBC,iv)

# do AES
ciphertext = cryptor.encrypt(pad(text))

#因为AES加密时候得到的字符串不一定是ascii字符集的，输出到终端或者保存时候可能存在问题
#所以这里统一把加密后的字符串转化为 base64 或者 16进制字符串

rew = base64.b64encode(ciphertext)

# md5
md = hashlib.md5()

sign = 'H*a1CsCDFIJIL*Ib'+rew.decode()+'bim09e0d656374f46228c5205fe66f06ba7'

md.update(sign.encode('UTF-8'))

print(md.hexdigest())

```

我大PHP very easier 

```php
    static function encrypt($data,$key,$iv)
    {
        $cipher = MCRYPT_RIJNDAEL_128;
        $mode = MCRYPT_MODE_CBC;
        $data=json_encode($data);
        $encrypt_data = mcrypt_encrypt($cipher, $key, $data, $mode, $iv);
        $data= base64_encode($encrypt_data);
        return $data;
    }

```
