---
title: 代码整洁之道之对象间的发布订阅
date: 2019-11-30 22:19:37
tags: 设计模式
categories: 设计模式
---

实现laravel中 `Observer`模式三种方式：

- 框架本地的Events事件机制
- PHP定义了两个接口：SplObserver和SplSubject
- 自定义`Observer` 和 `Subject`

不管采用哪种方式其目的是：对象更改其状态时，将通知附加的“观察者”。它用于缩短耦合对象的数量，并使用松耦合。

举个缓存依赖例子：:dog:

<!-- more -->

```php
//如果数据源发生变化根据cacheType清空不同数据缓存
class CacheRepository implements Cache {
    protected $catenation;

    public function __construct($db) {
        $this->catenation = $db;
    }

    public function cacheClear($cacheType) {
        //
    }
}

```

```php
//mem 观察员
class MemcacheObserver implements \SplObserver
{
    public function update(Cache $cache)
    {
       $cache->clean('mem');
    }

    /**
     * @return List[]
     */
    public function getArticles(): array
    {
        return $this->getArticles;
    }
}


```

<!-- more -->

```php
//mon 观察员
class MongodbObserver implements \SplObserver
{
    public function update(Cache $cache)
    {
       $cache->clean('mon');
    }

}

```

```php
//缓存对象
class CacheSubject implements \SplSubject
{

    /**
     * @var \SplObjectStorage
     */
    private $observers;

    public function __construct()
    {
        $this->observers = new \SplObjectStorage();
    }

    public function attach(\SplObserver $observer)
    {
        $this->observers->attach($observer);
    }

    public function detach(\SplObserver $observer)
    {
        $this->observers->detach($observer);
    }

    public function notify(Cache $cache)
    {
        /** @var \SplObserver $observer */
        foreach ($this->observers as $observer) {
            $observer->update($cache);
        }
    }
}

```
```php
//更新数据源
class ArticleController 
{
    private $memcache;
    
    private $mongodb;
     
    private $cacheSub;
    
    public function __construct(CacheSubject $cacheSub )
    {
        $this->cacheSub = $cacheSub;
    }
    
    public function postUpdate($id)
    {
        //添加订阅对象，此处是主动添加观察者，理想状态应该是观察者主动来订阅，做成事件配置，notify去读取配置
        $this->cacheSub->attach(MemcacheObserver $this->memcache);
        $this->cacheSub->attach(MongodbObserver  $this->mongodb);
        //更新数据源
        self::update(['id'=>$id]);
        //通知订阅者
        $this->cacheSub->notify(Cache $cache);
    }
}
```