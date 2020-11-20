---
title: php7
date: 2016-11-07 22:13:18
tags: php7
categories: php
---

### php7 

排序

```php

	$games = ['Mass Effect','Super','Zelda','Fallout','Metal Grear'];

	usort($games,function($a,$b)){

		//return $a <=> $b; // if a<b return -1 , a=b return 0 ,a>b return 1
		  return $b <=> #a; 
		//return $a - $b ;
	}

	var_dump($games);

	//$games 适合于对象

```

三元声明更简单

```php

$name = 'lhw';

echo $name ?? "cxx";  //等价于 empty($name) ? $name : 'cxx' 


echo $_GET['name'] ?? 'cxx'; //等于 isset($_GET['name']) ? $_GET['name'] : 'cxx';

```

<!-- more -->

返回类型声明

```php

class User{}

interface SomeInterface{
	public function getUser() : User;
}

class SomeClass implements SomeInterface {
	public function getUser() : User
	{
		return new User;
	}
}

(new SomeClass)->getUser
```

```php

class User{}

function getUser($id) : User {
  if (userDoesNotExists($id)) return false;
  return User::find($id);
}

var_dump(getUser());

```

psr4 加载规范

```php
	//touch composer.json

	{
		"autoload" :{
			"psr-4":{
				"App\\":"./"
			}
		}

	}

	//然后 composer install

	//去创建几个 class

	// Person.php
	namespace App;

	class Person{}

	//Animal.php

	namespace App;
	class Animal {}

	//如何来使用

	//touch index.php

	require 'vendor/autoload.php';

	use App\Person;

	use App\Animal;

	//同空间下，整合如下

	use App\{
		Person,
		Animal,
		Foo\Bar\Baz
	}

```


匿名类的用法

```php

interface Logger {
	public function log($message);
}

class TerminalLogger implements Logger {

	public function log($message){
		var_dump($message);
	}
}

class Application {
	
	protected $logger;

	public function setLogger(Logger $logger){

		$this->logger = $logger;

		return $this;
	}

	public function action(){

		$this->logger->log('Doing things.');
	}
}

$app = new Application();

$app->setLogger(new TerminalLogger);

$app->action();

```

使用匿名类

```php

interface Logger {
	public function log($message);
}


class Application {
	
	protected $logger;

	public function setLogger(Logger $logger){

		$this->logger = $logger;

		return $this;
	}

	public function action(){

		$this->logger->log('Doing things.');
	}
}

$app = new Application();

//使用匿名类在 业务逻辑层 依赖注入一个具体
$app->setLogger(new class implements Logger{
	
	public function log($message){

		var_dump($message);

	}

});

$app->action();

```

laravel 中的例子

```php
	
	class BookTransformer {

		public function transform(){
			
			return [];

		}
	}

	class Item {

		public function __construct($transformer){



		}

	}

	$item = new Item(new BookTransformer);

```

使用匿名类

```php
	

	class Item {

		public function __construct($transformer){



		}

	}

	$item = new Item(new class{

		public function transform(){

			return [] ;

		}

	});


```
