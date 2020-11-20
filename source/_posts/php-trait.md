---
title: php-trait
date: 2016-11-30 21:42:12
tags: trait
categories: php
---

### 单例模式

如果class new 起来嫌麻烦，:bikini: 或者想使用单例模式，请在你的class中  use Singleton

```php
trait Singleton
{
    protected static $__instances__ = array();
    protected function __construct()
    {
    }
    public function __clone()
    {
        throw new \Exception('Cloning '.__CLASS__.' is not allowed');
    }
    public static function getInstance()
    {
        $class = get_called_class();
        if (!isset(static::$__instances__[$class])) {
            static::$__instances__[$class] = new static();
        }
        return static::$__instances__[$class];
    }
    public static function resetInstance()
    {
        $class = get_called_class();
        unset(static::$__instances__[$class]);
    }
}

```

```php

class Order extends \AbstractOrder
{
	use Singleton

	public function getOrderInfo(){
		//todo
	}

}

Order::getInstance()->getOrderInfo();

```

### laravel 多太关联结合trait
