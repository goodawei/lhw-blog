---
title: laravel-设计模式
date: 2016-11-18 23:03:31
tags: 设计模式
categories: 设计模式
---

![设计场景][1]

[1]: http://o8979n2hu.bkt.clouddn.com/wcg.jpg

### 容器注入 

关键词： `依赖倒置`,`依赖注入`

相当于Yii中的DI容器，使用目的，由于在我们平时写代码过程中使用new这个关键字被视为一种不好的习惯，所以laravel中提供了两种类型（多种方法）来帮我们自动实例化类。

第一种方式：PHP 反射机制

laravel 会在实例化一个类触发了构造方法的时候检查构造方法所需要的依赖，并且自动实例化。不是全局的，根据业务场景去设计。



第二种方式：全局的 App:bind() 和 serviceProvider 方法 如：

```php
	
	class Foo{

	}

	App::bind('foo',function(){
		return new Foo();
	})

	app()->make('foo')->method();

```

laravel中是用serviceProvider中的register方法 来帮顶我们需要的依赖单元 如

```php
	
	protected function register(){

		$this->app->singleton('file',function(){
			return new Files;
		});
	}

```
<!-- more -->

而所有的 serviceProvider 都会中laravel 的app.config 下配置，这样就可以通过多种方式来直接使用这个类类(php  artisan make:privace 注册的服务)。


```php

 //app() 此方法在入口文件 index.php 中

 app()->make('file')->method();

 app()['file']->method();

 app('file')->method();

 //通过门面方式访问

 file::method();

```

使用 `serviceProvider`的好处，如果这个类依赖过多的依赖单元可以种register的时候一次处理好，不用业务层每次重新构造。



### 门面 Facade

laravel 中提供了一个facade服务层来共应用成可以使用静态方法一样访问类中的成员方法

```php
    public static function __callStatic($method, $args)
    {
        /*
         * 这个方法并不是laravel中 facade 中的，演示而已，new static()通常会替换成子类中构造注入或者方法注入的一个依赖，所以
         * 这里有个问题一直是个疑惑，new static() ，在这里会注入依赖显然不合理，不知道，jwt第三方是怎么做的
         */
        $method = 'dep'.ucfirst($method);
        if (method_exists(new static(), $method)) {
            return call_user_func_array([new static(), $method], $args);
        }
        throw new \BadMethodCallException("Method [$method] does not exist.");
    }

```


