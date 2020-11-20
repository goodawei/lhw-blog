---
title: swoole 安装
date: 2017-04-19 22:59:56
tags: swoole
categories: swoole
---

### 从release上选择一个版本clone源代码 :wink:

`sudo  git clone https://github.com/swoole/swoole-src.git`
  
### 编译生成configure文件

一 ： 通过phpize脚本生成
 `cd swoole-src.git`
 `sudo /usr/bin/phpize7.0`
  
如果没有找到`phpize` 先安装`phpize`

```php
sudo apt-get install autoconf automake libtool m4
sudo apt-get install php7.0-dev
For PHP7.1, use php7.1-dev
```
<!-- more -->

二 ：  通过configure生成`makefile` 文件

```php 
sudo ./configure 
```

三 ：通过 makefile 文件编译生成 `.so`文件

```php
sudo make
sudo make test  //可选
sudo make install
```
四 ：配置 `.ini`

```php
cd /etc/php/7.0/mods-available/
touch swoole.ini
extension=swoole.so

sudo ln -s /etc/php/7.0/mods-available/swoole.ini/etc/php/7.0/cli/conf.d/swoole.ini

sudo ln -s /etc/php/7.0/mods-available/swoole.ini/etc/php/7.0/fpm/conf.d/swoole.ini

//fpm模式下需要重启fpm，phpinfo 查看
sudo service php7.0-fpm restart

php -m | grep swoole
```

### 通过Cmake安装

把`swoole.so`做为一个C库
