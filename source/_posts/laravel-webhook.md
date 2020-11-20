---
title: 阿里云站点部署
date: 2016-10-13 11:53:34
tags: webhook
categories: webserver
---

# 以 Ubuntu 14.04 为例

### 通过下面的命令来删除Apache

```bash
sudo service apache2 stop
update-rc.d -f apache2 remove
sudo apt-get remove apache2
```

删除完之后，更新一下包列表

```bash
sudo apt-get update
```
### 安装Nginx

```bash
# 注意nginx安装需要第三方库的依赖，通常有 pcre库（支持rewrite模块）zlib库（支持gzip模块）和openssl库（支持ssl模块） 
sudo apt-get install nginx
```
安装完Nginx，执行｀sudo service nginx start｀再在浏览器地址栏输入你的公网IP，你就可以看的welcome to Nginx的界面了

### 安装PHP

```php
sudo apt-get install php5-fpm php5-cli php5-mcrypt
```
只有通过php5-fpm，PHP在Nginx下才能正常运行，遂，安装之。

至于php5-mcrypt，有些PHP框架会依赖于这个，比如Laravel就是，所以也把它装上了。

### 配置php

```php
sudo vim /etc/php5/fpm/php.ini
```
打开PHP配置文件，找到cgi.fix_pathinfo选项，去掉它前面的注释分号;，然后将它的值设置为0,如下 `cgi.fix_pathinfo=0`

启用php5-mcrypt: `sudo php5enmod mcrypt`

重启php5-fpm: `sudo service php5-fpm restart`

<!-- more -->

### 在搭建完LEMP环境之后，首先要明确两个重要目录 

Nginx的默认root文件夹 `/usr/share/nginx/html`

Nginx的服务器配置文件所在目录 `/etc/nginx/sites-available/`

### 部署Laravel

创建网站的根目录

`sudo mkdir -p /var/www`

配置nginx服务器

`sudo vim /etc/nginx/sites-available/default`

打开nginx的配置文件之后，找到server这一块，大概是长这个样子的

```bash
server {
        listen 80 default_server;
        listen [::]:80 default_server ipv6only=on;

        root /usr/share/nginx/html;
        index index.html index.htm;

        server_name localhost;

        location / {
                try_files $uri $uri/ =404;
        }
}
```

其中root，index ，server_name和location这几行需要稍微修改一下

```bash

server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root /var/www/laravel/public;
    index index.php index.html index.htm;

    server_name server_domain_or_IP;      # 将server_domain_or_IP修改为你的公网IP

    location / {
            try_files $uri $uri/ /index.php?$query_string;
    }
}
```

最后我们还需要配置一下Nginx，让其执行PHP文件。同样是在这个文件里，在location下方添加下面的配置：

```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root /var/www/laravel/public;
    index index.php index.html index.htm;

    server_name server_domain_or_IP;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

配置完之后重启Nginx，使上面的配置项生效。

`sudo service nginx restart`

### 创建Laravel项目 

第一种方式：

直接通过composer来安装,安装composer

```bash
cd ~
curl -sS https://getcomposer.org/installer | php
```
如果 curl 有关于ssl错误可以尝试一下方式：

```php
wget https://getcomposer.org/installer

and 

php installer;
```

composer全局使用：

`sudo mv composer.phar /usr/local/bin/composer`

或者

`sudo mv composer.phar /usr/bin/composer`

然后在/var/www目录下直接执行

`sudo composer create-project laravel/laravel laravel`

第二种方式：

使用Git和Coding平台

安装git

`sudo apt-get install git`

安装完成就可以使用git了，然后在Coding上创建一个私有项目laravel，里面包含所有该Laravel项目所需代码。

一旦本地代码都推送到Coding，然后在/var/www目录下直接使用

`git clone your-project-git-link`

your-project-git-link替换为你Coding上的laravel项目地址

注意：

不管哪种方式安装的代码，/var/www/都是属于root用户的，而访问网站的用户则需要正确的权限和访问限制，我们可以通过下面的命令来实现。

`sudo chown -R :www-data /var/www/laravel`

根据Laravel的官方文档，/var/www/laravel/storage目录需要给网站的用户写权限

`sudo chmod -R 775 /var/www/laravel/storage`

同时还要执行以下操作

```php
php artisan cache:clear  //在bootstrap/cache 生成 services.php 文件

php artisan key:generate //将生成的key复制到config/app.php替换82行的APP_KEY键值。
```

### ssh 登录服务器

`ssh-keygen -t rsa` 本地生成秘钥对

接下来需要将本地的 id_rsa.pub 拷贝服务器上。

ssh登录服务器创建 .ssh 目录

```bash
cd ~
sudo mkdir .ssh
```
切回到本地 .ssh 目录

`scp id_rsa.pub root@123.98.35.77:~/.ssh/id_rsa.pub`

输入服务器密码拷贝

拷贝成功回到服务器

`cat id_rsa.pub >> authorized_keys`

最后删掉 id_rsa.pub ,退出服务器，重新登录，就不用输入密码了。

### 利用webhook 实现PHP 自动部署 git 代码

首先推荐两篇文章

http://m.aoh.cc/149.html

http://overtrue.me/articles/2015/01/how-to-deploy-project-with-git-hook.html

登录服务器部署用户公钥

`ssh-keygen -t rsa -C "bjletaoba@sina.com"` 

将用户公钥部署到codding 的用户设置中

创建目录

`sudo mkdir /var/www/.ssh`

修改所属主 (针对 nginx 的)

`sudo chown -R www-data:www-data /var/www/.ssh/`

部署项目公钥 (针对 nginx,注意 id_rsa 的权限必须不能上777 或者 755 ，可以上700)

`sudo -Hu www-data ssh-keygen -t rsa `//注意所在的当前目录必须在 www下

部署到 codding 的项目中

`sudo cat /var/www/.ssh/id_rsa.pub`

准备部署代码和注册路由

路由要注册到 middleware 外面

```php
	Route::group(['middleware'=>['web']],function(){


	})

	Route::any('/deploy','DeployController@deploy');
```

```php
    public function deploy(Request $request){
        $token    = 'ffanfood';
        $target   = '/var/www/ffanfood/aliyun'; // 生产环境web目录
        $wwwUser  = 'www-data';
        $wwwGroup = 'www-data';
        $json = json_decode(file_get_contents('php://input'),true);
        if(empty($json['token']) || $json['token'] !== $token){
            exit('error_request');
        }
        file_put_contents('deploy.log',var_export($json,true),FILE_APPEND);
         $cmds = array(
             "cd $target && git pull",
             "chown -R {$wwwUser}:{$wwwGroup} $target/",
         );
         foreach ($cmds as $cmd) {
             shell_exec($cmd);
         }
    }
```
 以上代码可以使用 php-shell-wrapper 这个 包

 `https://github.com/adambrett/php-shell-wrapper`

 ```php
 	/**
     * 执行cd 命令
     * @param $paht
     * @return $this
     */
    protected function cdInfo($paht){
        $command = new Command('cd');
        $command->addParam(new Param($paht));
        $this->shell->run($command);
        return $this;
    }
 ```


修改git配置和保存git用户名密码 (会在 ／var/www 下生成git 配置文件)

```bash
sudo -Hu www git config --global credential.helper store # 永久保存
sudo -Hu www-data git config --global user.name lhw
sudo -Hu www-data git config --global user.email bjletaoba@sina.com
```

初始化 (注意要用ssh 的链接 这样就不用输入密码)

`sudo -Hu www-data git clone git@git.coding.net:goodawei/aliyun_laravel_5_2.git  --depth=1`


