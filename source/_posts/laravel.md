---
title: laravel_install
date: 2016-06-06 22:38:48
tags: laravel
categories: laravel
---

运行以下命令安装 `Composer`：

```php
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

php -r "if (hash_file('SHA384', 'composer-setup.php') === '070854512ef404f16bac87071a6db9fd9721da1684cd4589b1196c3faf71b9a2682e2311b36a5079825e155ac7ce150d') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

php composer-setup.php

php -r "unlink('composer-setup.php');"
```
<!-- more -->

一共4条命令，它们分别执行以下的操作：

下载安装文件到当前目录。

检验安装文件的SHA-384散列。

运行安装文件。

删除安装文件。

然后我们就可以运行执行以下命令来运行Compoer了：

    $ php composer
    
全局安装:

所谓全局安装，其实就是把Composer安装到PATH变量里的某个目录中，这样你就可以从任何地方去访问Composer了。个人也建议这么做。我们只需要把本地安装的composer执行文件移动到全局目录下即可：

    $ mv composer.phar /usr/local/bin/
    
然后安装(最好开个vpn) 执行：

    composer create-project laravel/laravel laravel5_lhw

两种启动服务方式：

一 使用PHP内置服务器:

    php -S localhost:8999 -t public

二 laravel 命令行:

    php artisan serve 

三 使用apache 

`apache`配置支持 `.htaccess`

```php
<Directory />
    AllowOverride All
    Require all granted
</Directory>

<Directory "/Library/WebServer/Documents">
	 AllowOverride All
</Directory>

```
编译安装 PHP apache 配置：`AddHandler application/x-httpd-php .php`
