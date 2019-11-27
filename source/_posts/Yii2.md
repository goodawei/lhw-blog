---
title: Yii2
date: 2016-05-13 15:17:31
tags: Yii
categories: Yii
---

# Yii2 登录机制和验证码的使用

---

### :chipmunk: Yii2登录

如果要通过gii自定义生产user模型的话(不适用默认的user)，要在当前应用的config/main 下配置user组件路径如：
```php
'user' => [
        'identityClass' => 'common\models\user\Diyouuser',
        'enableAutoLogin' => true,
    ],
```
	接下来在`loginForm` 模型下 `use common\models\user\Diyouuser;` 使用该模型

	验证规则写在 loginForm 下 如：

<!-- more -->

```php

public function rules()
{
    return [
        [['username', 'password'], 'required'],
        ['password', 'validatePassword'],
    ];
}

public function validatePassword($attribute, $params)
{

    if (!$this->hasErrors()) {
        $user = $this->getUser();
        if (!$user || $user->password != md5($this->password)) {
            $this->addError($attribute, '用户名或者密码错误');
        }
    }
}
```

在触发登录前要先验证`validatePassword`的话 必须触发 `Yii`的 

    $model->validate()

如果要添加自定义`validate`的话，也必须在触发login前调用自定义的`mycustvalidate`，
`validate` 都验证通过后 在触发 Yii 的 login :

     Yii::$app->user->login($user);

`$user` 是  `common\models\user\Diyouuser;`组件的一个组件而且必须实现 IdentityInterface 这个接口

### Yii2 验证码相关
使用验证码步骤:

 1. 在使用的相关类中重写 Controller中的actions方法主要是自定义配置。
 2. 要做Model的验证规则中指定使用的路径位置如 :
 `['verify', 'captcha','captchaAction'=>'login/captcha']`
 3. 在视图中使用widget 部件 如：

 ``` php
<?= $form->field($model, 'verify', [
    ])->widget(yii\captcha\Captcha::className(), [
        'template' => '<div class="input-group">
                            <span class="input-group-addon">
                                <span class="glyphicon glyphicon-picture"></span>
                            </span>{input}
                            <span class="input-group-addon"style="margin:0;padding:0">
                                    {image}
                                    </span>
                                </div>',
                            'imageOptions' => [
                                'alt' => '点击换图',
                                'title' => '点击换图',
                                'style' => 'cursor:pointer'
                            ],
                            'captchaAction' => 'login/captcha',
     ])->label(false) ?>
 ```
 
### Yii2 中的分页

``` php
<nav>
<?php
$this->widget('CLinkPager',array(
    'header'=>'',
    'firstPageLabel' => '首页',
    'lastPageLabel' => '末页',
    'prevPageLabel' => '上一页',
    'nextPageLabel' => '下一页',
    'pages' => $pager,
    'maxButtonCount'=>8,
    'cssFile'=>false,
    'htmlOptions' =>array("class"=>"pagination"),
    'selectedPageCssClass'=>"active"
 )
 );
?>
</nav>
```

 1. 分页必须在`<nav>`里
 2. `htmlOptions`选项是必须的，他指定了yii生成的分页`div`的`class`名称，在这里我们使用bootstrap的class名
 3. selectedPageCssClass选项指定了当前选中页的样多，在这里我们使用bootstrap的active
 4. 另外还需要把cssFile设为false，不加载分页css样式文件

---

# Yii2事件

---
### Yii2中的事件原理
`Yii2`中的事件`handle`,就是一段回调处理程序，说白了`handle`就是回调函数的应用，php中回调的应用几种方式：

 一. 一个全局的函数 如

 
```php
 call_usr_func('funcname','args')
 $person = new Person;
 // 使用PHP全局函数作为handler来进行绑定
 $person->on(Person::EVENT_GREET, 'person_say_hello');
```
 二. 对象中的成员方法如果:

```php
// 使用对象$obj的成员函数say_hello来进行绑定
$person->on(Person::EVENT_GREET, [$obj, 'say_hello']);
```

三.类中的静态方法：

```php
// 使用类Greet的静态成员函数say_hello进行绑定
$person->on(Person::EVENT_GREET, ['app\helper\Greet', 'say_hello']);
```

四. 匿名函数：

```php
// 使用匿名函数
$person->on(Person::EVENT_GREET, function ($event) {
    echo 'Hello';
});

```
`Yii` 在绑定事件的时候 其实就是 维护一个 `_event` 事件数组，通过`on` 方法 按照规则 将` handle`和`args`插入到事件数组里，当调用`trigger`时候，其实就是循环 `_enevt` 然后 通过 `call_user_func_array()`触发 回调，回调的逻辑要在 ，定义好的 以上四种 中`handdle` 中实现。

---

# Yii2 Layout

---

### Layout布局
在`controall` 中使用布局文件方法：
```php
Public function init(){
	Parent:: init();
	//在当前controll 指定布局文件
	$this->layout = 'custmain'；
	//在当前controll不使用布局文件
	$this->layout = false;
}
```
然后在`controll `渲染视图前 在指定要重新定义的`layout`
```php
Public function actionTest(){
	$this->layout = 'main'；
	$this->render();
}
```
布局文件可以嵌套使用：原理是从内往外逐层传递`$content` 变量
例如：
Testlayout 中指定
```php
<?php $this->beginContent('@/app/view/layout/main')  ?>

<div>
		Cust content
</div>

<?php $content  ?>

<?php $this->endcontent ?>
```
最终将`testlayout`文件作为一个`content`变量传递到`main`中的`content`

`View` 中可以指定多个 `block` 块 默认是一个 `content`,每个`block`款 要在`layout`布局文件中 用变量一一对应 如:

```php
//View 中指定两个 block
<?php  $this->beginBlock('content');?>

	<div>test</div>
	
<?php  $this->endBlock ();?>



<?php  $this->beginBlock('contenttwo');?>

	<div>test</div>
	
<?php  $this->endBlock ();?>

//那么在 laoyout 中main  文件中 要有两个变量 

<?php  echo $content ;?>

<?php  echo $contenttwo ;?>
```
# Yii2中 widget 和 assets 的使用：
### assets 的使用
使用 `assets` 资源控制器，可以达到按需加载`css`、`js`样式的目的，通常会将一个站点的公共部分提取出来，制作`layout`布局并且制作公共 `assets`，在`layout`的布局中将` assets`注册进来。
例如：
先定义一个 `MainAsset` 
```php
class MainAsset extends AssetBundle
{
    public $basePath = '@webroot';
    public $baseUrl = '@web';
    public $css = [
        'css/base.css',
    ];
    public $js = [

    ];
    public $depends = [
        'yii\web\YiiAsset',
        'yii\web\JqueryAsset',
        'backend\assets\FontAwesomeAsset',
        'yii\bootstrap\BootstrapAsset',
    ];

} 
```

然后在`layout`中这样使用 ：
```php
MainAsset::register($this); //默认会将css添加到header中将js添加到页尾
```
当有个别页面需要加载不同静态文件时，有两种引用方式：
一：在视图中通过`$this->registerJsFile()`引入可以指定依赖关系和加载顺序例如：
```php
 $this->registerJsFile('/js/editormd.js',['depends'=>['yii\web\JqueryAsset']]);
 $this->registerCssFile('/js/editormd.css',['depends'=>['yii\web\JqueryAsset']]);

```
如果在控制器中可以使用当前的视图对象如： 
```php
$view = $this->getView();
  $view->registerJsFile('/js/editormd.js',['depends'=>['yii\web\JqueryAsset']]);
  $view->registerCssFile($cssfile, [AppAsset::className(), 'depends' => 'frontend\assets\AppAsset']);
```

二：在单独制作出一个`authorassets`类
在页面中通过 `authorassets ::register($this);`注册进来

在控制器中：        
```php
$view = $this->getView();
authorassets::register($view);
```

在控制器中执行一段js代码：
```php
$view->registerJs('var '.$handle.' = editormd('.$options.');');
```

### widget的使用
`widget`大部分都是在`view`中使用 分为两种情况：
一：通过数据模型的`ActiveField`调用
```php
<?=$form->field($model,'markdown')->widget('common\widgets\EditorMD',[
    'editorConfigs'=>[
    'height'=>500,
    'markTextarea'=>'Article[markdown]',
    'htmlTextarea'=>'Article[content]',
    'saveHTMLToTextarea'=>true,
    ],
    'editorOptions'=>[
    'style'=>'z-index:1000;',
    ]
])?>
```
在`ActiveField`中会自动触发我们自定义的`widget`如：
```php
public function widget($class, $config = [])
{
    /* @var $class \yii\base\Widget */
    $config['model'] = $this->model;
    $config['attribute'] = $this->attribute;
    $config['view'] = $this->form->getView();
    $this->parts['{input}'] = $class::widget($config);

    return $this;
}
```
二：直接使用
```php
 <?= \backend\widgets\UploadWidget::widget(['id' => 'icon_5', 'upload_success' => 'upload_success']) ?>
```
设计`widget`的步骤：

定义属性：调用`widget`的时候传的属性值要在`widget`中先定义

`widget`调用初始化时`init()`的逻辑

最后`run()`进行输出。


