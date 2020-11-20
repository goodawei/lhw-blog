---
title: Yii_install
date: 2016-05-17 21:37:39
tags: Yii
categories: Yii
---
# 用composer 安装Yii2
安装依赖包方式，获取定义的依赖到你的本地项目，只需要调用 composer.phar 运行 install 命令如。
```php
php composer.phar install
```

安装时需要`githug`账号，下载时需要token,可以通过 `git  ssh-keygen` 生成 token秘钥

一 先下载 advanced 模板  https://github.com/yiisoft/yii2-app-advanced
二 dos cd 到 advanced 模板下 ：执行
```php
php -r "readfile('https://getcomposer.org/installer');" | php   
//下载 composer.phar 
```
三 准备插件 
```php
php composer.phar global require "fxp/composer-asset-plugin:1.0.0"
```
或者
```php
 php composer.phar global require "fxp/composer-asset-plugin:~1.1.1"
```
<!-- more -->

要保证 composer 版本一致 升级 1.1.1 更新命令  
```php
composer self-update
composer update
```
四  安装
```php
php composer.phar create-project --prefer-dist --stability=dev yiisoft/yii2-app-advanced  ../yii2test
```
五 最后一步：初始化我们的应用,初始化是在你创建的yii2test目录下 执行 init命令 选0 按y

最后Yii2 添加新应用需要注意的地方 以新加wap 站为例

一  将 frontend 前台复制一份 改名wap(注意把里面所有文件的命名空间改为 wap的)
二  将common/config bootstrap 新加一份 wap 的别名
三  将common/config main.php  新加路由修改配置（不使用默认路由规则）
    将改过来的wap 下的config 的main.php 中的 `id` 和`controllerNamespace` 等改正确
```php
'urlManager' => [
    'enablePrettyUrl' => true,
    'showScriptName' => false,
]
```
四：修改apache 路由规则 隐藏点 index.php 
  RewriteEngine on
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule ^(.*)$ index.php/$1 [QSA,PT,L]

