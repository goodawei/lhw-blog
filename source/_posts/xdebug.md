---
title: phpstorm vagrant xdebug
date: 2016-11-13 20:57:35
tags: xdebug
categories: bug
---

# phpstorm 结合homestead 配置xdebug :high_heel:


### 编辑php配置文件 

`homestead ssh`

`sudo vi /etc/php/7.0/fpm/conf.d/20-xdebug.ini`

添加 ：

```php

zend_extension=/usr/lib/php/20151012/xdebug.so
xdebug.remote_enable = 1
xdebug.remote_connect_back = 1
xdebug.remote_port = 9000
xdebug.scream=0
xdebug.cli_color=1
xdebug.show_local_vars=1

```

重启fpm

`sudo service php7.0-fpm restart`

<!-- more -->

### 下载chrom  xdebug-help  扩展

https://chrome.google.com/webstore/detail/eadndfjplgieldjbigjakmdgkmoaaaoc


配置xdebug-help：

IDE key  ： phpstorm


### phpstorm 配置

呼出配置项，搜索php，选择一个版本。

配置`ssh`链接vagrant ,如果使用私钥链接位置在 `.homestead\.vagrant\machines\default\virtualbox`

如果有问题可以查看 `phpinfo xdebug`是否安装成功


### 调试

打开 `phpstorm xdebug` 监听。

打开 `xdebug-helper` 扩展。

输入`app.dev`，phpstorm会弹出对话框，选择“Accept”。

然后选择 `Preferences —> Languages & Frameworks — > PHP —> Servers`

这时我们需要修改项目外层路径对应的映射关系，否则就只能对index.php进行debug，而其他文件的断点将不会起作用

监听多个域名，要做server 下配置，并且初次访问，必须访问index.php
