---
title: Yar
date: 2017-03-27 22:57:58
tags: yar
categories: php
---

### :pig: Yar 编译安装

```php

git clone https://github.com/laruence/yar.git

cd yar

//安装依赖包
sudo apt-get install curl
sudo apt-get install libcurl4-gnutls-dev

//软连接到bin目录
ln -s /usr/local/php7.0/bin/phpize /usr/bin/phpize 

phpize
./configure
sudo make
sudo make install

//配置fpm
sudo ln -s /etc/php/7.0/mods-available/yar.ini  /etc/php/7.0/fpm/conf.d/yar.ini

//重启fpm

sudo service php7.0-fpm restart

```

<!-- more -->

### 服务端注册

```php
class ApiController
{
    public function getUserInfo($uid)
    {
        return json_encode([
            'uid'=>$uid,
            'username'=>'lihongwei',
        ]);
    }
    public function getDealInfo($dealid)
    {
        return json_encode([
            'did'=>$dealid,
            'dealname'=>'haihang',
        ]);
    }
}
$service = new \Yar_Server(new ApiController());
$service->handle();

```

### 客户端调用

```php

 //同步
 
 $client = new \Yar_Client("http://yaf.dev");
 /* 以下是最佳setopt */
 $client->SetOpt(YAR_OPT_CONNECT_TIMEOUT, 1000);
 /* 同步调用远程服务 */
 $result = $client->getUserInfo(100);

 //异步
 
\Yar_Concurrent_Client::call("http://yaf.dev", "getUserInfo",[11320],[$this , 'getUserInfoCallback']);

\Yar_Concurrent_Client::call("http://yaf.dev", "getDealInfo",[18800],[$this , 'getDealInfoCallback']);

\Yar_Concurrent_Client::call("http://yaf.dev", "getDealInfo",[18800],[$this , 'getDealInfoCallback'],[$this , 'getDealInfoCallbackError'],[YAR_OPT_PACKAGER => "json"]);

\Yar_Concurrent_Client::call("http://yaf.dev", "getDealInfo",[18800],[$this , 'getDealInfoCallback'],[$this , 'getDealInfoCallbackError'],[YAR_OPT_TIMEOUT=>1]);

\Yar_Concurrent_Client::loop();

public  function getUserInfoCallback($retval, $callinfo) {
    $data =  json_decode($retval ,true);
    var_dump($data['username']);
}

```



