
commit : https://gitlab.mfwdev.com/WebDev/sales/merge_requests/7788/diffs

领券封装思路：

1。领券表现，每个会场都有自己类型的优惠券，每种券有自己的领取规则（黑名单，并发，是否标记设备，单领，批量领。。。）

2。把不变的部分封装起来，变化的部分注入进来。

3.所以抽象出优惠券概念，将领券逻辑封装在基类 它依赖一组 request 和 validator 的抽象。

4。由前端发出领取类型然后 通过工厂创造出一组 request 和 validator 实例注入领券模型。

目的：领券框架尽可能是领券部分业务逻辑不在发生变化，而用 request 和 validator 来应对变化。

request 和 validator 可扩展 可复用 可组合，能够做的 针对扩展开放，修改关闭。避免修改带来风险。


show code.





路由中间件：

1. 使代码分层和指责更加清晰。
2. 松耦合，避免一些业务逻辑和主业务流程耦合在一起，导致代码臃肿，可读性差，不能舒畅的进行迭代和更新。
3. 中间件可复用，可扩展，使代码组织更加灵活。

它是一种管道模式的实现，请求示意图：

```php
        中间件前置部分                  中间件后置部分
(begin) ----------------> function() -----------------> (end)
            ^   ^   ^                   ^   ^   ^
            |   |   |                   |   |   |
            |   |   +------- M1() ------+   |   |
            |   +----------- ...  ----------+   |
            +--------------- Mn() --------------+

```

show case:

show code:

1. yield 概念。

很多语言都有yield关键字，php中它可已是迭代器，也可以当作携程来使用，这里的携程是程序上下文的切换，比线程的上下文切换开销更小。

它有自己特殊的堆线结构，可以记录程序的执行状态。这也是他在循环处理大的数据集的优势，它不用将这个数据读到内存，而是逐条来取。

这个中间件其实是利用了 yield 切换上下文实现的。

```php

//首页通过middleware方法注册要使用的中间件，这里是个外观模式
\Ko_Web_Route::middleware(['auth','playDot','businessLock'])->VPost('henry',function (){
    echo '业务处理逻辑！';
});

```

```php

    private static function _VRegisterRoute($sMethod, $sName, $fnRoute)
	{
	    //ko路由注册表
		self::$s_aRoute[$sName][$sMethod] = $fnRoute;
	}
	
	private static function _IWebRoute()
	{
		if (!is_file(self::$s_sFile))
		{
			return self::$s_iErrno = self::ERR_FILE;
		}
		self::_VRequireFile(self::$s_sFile);
		if (!isset(self::$s_aRoute[self::$s_sFunc]))
		{
			return self::$s_iErrno = self::ERR_FUNC;
		}
		if (!isset(self::$s_aRoute[self::$s_sFunc][self::$s_sMethod]))
		{
			return self::$s_iErrno = self::ERR_METHOD;
		}
		//执行中间件的handle
        $middlewareStack = [];
        if(!empty(static::$middleware)){
            foreach (static::$middleware as $item) {
                //这里定义一个规约，根据规约去找定义好的 门面
                $mid = apps\sales\apps\activity\MMiddleware_Middleware::$middlewareMap[$item];
                if(!empty($mid)){
                    $ref = new \ReflectionClass($mid);
                    $obj = $ref->newInstance();
                    if ($obj->handle() instanceof \Generator) {
                        $generator = call_user_func([$obj,'handle'],[]);
                        $middlewareStack[] = $generator;
                        //执行中间件上下文中的next上部分程序 前置中间件
                        $generator->current();
                    }
                }
            }
        }
        //从路由表里执行业务程序
		call_user_func(self::$s_aRoute[self::$s_sFunc][self::$s_sMethod]);
        //执行中间件上下文中的next下部分程序 后置中间件
        if(!empty($middlewareStack)){
            while ($generator = array_pop($middlewareStack)) {
                $generator->next();
            }
        }

		return self::$s_iErrno = 0;
	}

    /**
     * 中间件 handle
     * @param $middleware
     * @return static
     */
    public static function middleware($middleware)
    {
        static::$middleware = $middleware;
        return new static() ;
    }
```


vue : route  分离。


CodeReview

CodeReview的目的是提升代码质量，尽早发现潜在缺陷与BUG，降低修复成本，同时促进团队内部知识共享，帮助更多人更好地理解系统。

后端回顾总结：

    从代码组织结构上总结为以下几点：
        1.单一职责原则
            
        2.开闭原则
        
        3.面向接口编程
        
            
        
todo： 

1。安排一次编码规范讨论
    代码风格：可读性，命名，函数长度/类长度，注释，参数个数等。
    
2。定期安排一次代码最佳实践，支持现场重构代码。

前端回顾总结：

    Lucas 针对本次8.18大促的项目从系统设计和项目设计方面上总结出问题以及相关建议。  
    
    PC H5 未分离，项目、打包入口---发布系统
    外部依赖未提取
    版本锁定---发布系统
    图片在项目中
    base64
    文件名称
    打包加速
    代码 开启eslint检测
    
    html
    需要单独发到cdn，html直接引入
    <link rel="dns-prefetch" href="https://my-site.com">
    preload prefetch
    js跨域 crossorigin
    太长的需要换行
    实体字符使用
    空链接
    js
    工具引入
    jquery – 外部资源地址
    qs
    jquery.mCustomScrollbar
    
    lodash
    
    swiper
    
    weixin-js-sdk
    
    路由书写
    axios原生支持promise和拦截器，为什么要封装，拦截器中可以加入loading
    import 写法顺序
    document、window操作封装
    数据缓存 相同写法需要定义变量缓存
    api统一维护
    配置文件维护
    setInterval 用 setTimeout 模拟
    
    require.context
    
    const emptyPage = () => import(/* webpackChunkName: "emptyPage" */ '@/components/emptyPage');
    
    css
    书写规范
    命名
    顺序
    层级
    格式统一

