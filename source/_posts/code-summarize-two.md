---
title: 代码整洁之道之优化嵌套代码
date: 2019-11-30 20:44:30
tags: 设计模式
categories: 设计模式
---

# :shamrock: if else 和 switch case 优化方案

1 如果嵌套逻辑是 返回值或者输出等简单的一句话代码，则使用表驱动方案。

2 如果是逻辑处理或者函数调用，则采用策略和多态模式。

举个例子：


```php
        $type = $data['type'];
 
        //1-绑卡 2-充值
        switch ($type) {
            case 1:
                $res = (new AccountPermission())->bindCard($account);
                break;
            case 2:
                $res = (new AccountPermission())->recharge($account);
                break;
        }

```
<!-- more -->

以上这个例子逻辑大概就是根据客户端传递的type去调用不同的验证方法，这样写违背了设计原则之中的针对扩展开放，修改关闭原则，如果有新增type类型那么必须要修改此文件和AccountPermission文件，另外函数的封装要尽可能的`短小精悍`，代码逻辑直截了当并尽可能的减少依赖关系，这样才能体现出只做好一件事情。那么针对这些问题，以上的代码优化思路是这样的：

1 定义抽象类或者接口 AbstractPermission
```php
    abstract class AbstractPermission
    {
        abstract public function validator(...$data);
    }
```
2 创建不同的验证实体去实现或者继承AbstractPermission
```php
    //BindCard的验证实体
    class BindCardPermission extends AbstractPermission
    {
            public function validator(...$parms){
                //todo
            }
    }
```
<!-- more -->

```php
    //Recharge的验证实体
    class RechargePermission extends AbstractPermission
    {
            public function validator(...$parms){
                //todo
            }
    }
```
3 创建工厂将创建实体的过程封装起来，在业务层通过策略来动态获取实例对象

```php
    //当然此处的工厂实例最好也要去继承或者实现一个抽象工厂
    class FactoryPermission
    {
        const PERMISSION = 'Permission';
        public static function permission($permission,$parms){
            $class = $permission. constant(__CLASS__ . "::PERMISSION");
            $permissionObj = "App\\{$class}";
            return call_user_func_array(
             [new $permissionObj(),'validator'],                                      [$parms]);
        }
    }
```

4 如何使用，这里就要去设计一套策略了，可以结合创建工厂的步骤设计具体的策略

```php
     //  type 传递 BindCard  Recharge ....
     $type  = $data['type'];
     $parms = $data['parms'];
     $res = FactoryPermission::permission($type,$parms)
```

总结：将职责单一，减少依赖，针对扩展开放修改关闭，这样以后有新增type只需要扩展新增的permission实体，而不需要修改任何其他文件。
