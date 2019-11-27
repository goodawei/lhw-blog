---
title: Ubuntu 14 PHP 5.6 升级到PHP 7.0
date: 2016-10-24 12:25:28
tags: php7
categories: php
---

### Ubuntu 14 PHP 5.6 升级到PHP 7.0

备份之前的配置，备份之前的配置，备份之前的配置 ！！！

适用于Ubuntu 14 版本的VPS，Ubuntu 16 版本貌似直接就包含了php7.0

1.先解决有可能存在的语言问题

执行

```php
sudo apt-get update 
sudo apt-get install -y language-pack-en-base
```
然后命令行配置：

```php
locale-gen en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```
2.删除原来的php5.6

```php
 sudo apt-get purge php5-fpm && apt-get --purge autoremove
```

3.添加php7.0的仓库

```php
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ondrej/php
```
<!-- more -->

4.安装php7.0

```php
sudo apt-get update 
sudo apt-get install php7.0
sudo apt-get install php7.0-mysql
sudo apt-get install php7.0-fpm

sudo apt-get install php7.0-curl php7.0-xml php7.0-mcrypt php7.0-json php7.0-gd php7.0-mbstring
```

上面可以一步一步来安装，如果出现依赖冲突的情况，请一个一个安装。

5.配置新的php.ini

```php
sudo vim /etc/php/7.0/fpm/php.ini
#将cgi.fix_pathinfo=1这一行去掉注释，将1改为0。
```

6.配置php-fpm

```php
sudo vim /etc/php/7.0/fpm/pool.d/www.conf
#  配置这个 listen = /var/run/php7.0-fpm.sock
```

7.nginx 配置

```php
sudo vim /etc/nginx/sites-enabled/default
```

配置相对应的路径和 location,(以laravel项目为例)：

```php
listen 80 default_server;
listen [::]:80 default_server ipv6only=on;

root your_website_root;
index index.php index.html index.htm;

# Make site accessible from http://localhost/
server_name your_domain;

location / {
	# First attempt to serve request as file, then
	# as directory, then fall back to displaying a 404.
	try_files $uri $uri/ /index.php?$query_string;
	# Uncomment to enable naxsi on this location
	# include /etc/nginx/naxsi.rules
}
location ~ \.php$ {
	try_files $uri /index.php =404;
	fastcgi_split_path_info ^(.+\.php)(/.+)$;
	fastcgi_pass unix:/var/run/php7.0-fpm.sock;
	fastcgi_index index.php;
	fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	include fastcgi_params;
}
```

注意这里的 fastcgi_pass unix:/var/run/php7.0-fpm.sock; 要和第6步的 php-fpm 配置：/var/run/php7.0-fpm.sock; 一致。

如果使用 https 的话，推荐：

```php
	ssl on;
	ssl_certificate /etc/nginx/ssl/ssl.crt;
	ssl_certificate_key /etc/nginx/ssl/ssl.key;

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
	ssl_prefer_server_ciphers on;
```

开启 gzip 可参考，配置文件位于 nginx.conf：

```php
	gzip on;
	gzip_disable "msie6";

	gzip_vary on;
	gzip_proxied any;
	gzip_comp_level 5;
	gzip_min_length 256;
	gzip_buffers 16 8k;
	gzip_http_version 1.1;
	gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

```
