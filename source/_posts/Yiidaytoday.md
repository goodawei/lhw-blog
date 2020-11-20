---
title: Yii匿名函数
date: 2016-05-28 12:34:35
tags: Yii
categories: Yii
---

---

# Yii2匿名函数小应用

---

```php
namespace console\controllers;

use Yii;

class TestController extends \yii\console\Controller
{

    public $enableCsrfValidation = false;

    public function actionTest()
    {

        //在循环中使用闭包时,要注意如果一个变量在闭包中改变了值,到下一次循环时,不会被释放
        $type = [];
        $flg  = 0; //要改变的变量 或者对象
        $data = [
            ['type'=>'1','one_level'=>100,'two_lever'=>200,'phone'=>'15510337665' ],
            ['type'=>'1','one_level'=>100,'two_lever'=>200,'phone'=>'15510337665' ],
            ['type'=>'2','one_level'=>100,'two_lever'=>200,'phone'=>'15510337665' ],
            ['type'=>'1','one_level'=>100,'two_lever'=>200,'phone'=>'15510337665' ],
            ['type'=>'2','one_level'=>100,'two_lever'=>200,'phone'=>'15510337665' ],
            ['type'=>'1','one_level'=>100,'two_lever'=>200,'phone'=>'15510337665' ],
            ['type'=>'2','one_level'=>100,'two_lever'=>200,'phone'=>'15510337665' ],
        ] ;

        foreach($data as $key=>$val){

            $this->filter($val , $flg ,function($ret) use ($type){

                file_put_contents('lhw.log',var_export($ret,true).PHP_EOL,FILE_APPEND);

            });
        }

    }

    private function filter(array $arr,$flg,$fun)
    {

        if($arr['type'] == '1'){
            $flg = 500; //下次循环500不会从内存中释放
            $arr['one_level'] = $arr['one_level'] + 1000;
            $arr['flg'] = $flg + $arr['one_level'];
        }

        $fun($arr);

    }


    //使用过滤
    public function actionFilter()
    {

        //习惯使用list 指定获取索引数组值
        list($value, $property, $cType) = ['dd','33','32'];

        //使用匿名函数过滤元素
        $filterItem['andor'] = 'or';//过滤元素
        $andor = call_user_func(function($default) use($filterItem) {
            if ((array_key_exists('andor', $filterItem)) && (strtolower($filterItem['andor']) == 'or')) {
                return "OR";
            }
            return $default;
        }, "AND");

        //结合使用
        list($val) = [call_user_func(function($default) use ($filterItem){
            if ((array_key_exists('andor', $filterItem)) && (strtolower($filterItem['andor']) == 'or')) {
                return "OR";
            }
            return $default;
        },'default')];

        echo $val;
    }


    //如果整个action 都要求返回json 可以在进action 之前 判断转换如：
    public function beforeAction($action)
    {

        if (Yii::$app->request->isAjax) {

            Yii::$app->response->format = Response::FORMAT_JSON;

        }

        return   parent::beforeAction($action);//必须return
    }

    public function actionApi()
    {
        return ['name'=>'lwh'] ;//自动转换成json
    }


    //使用模型对象添加数据的一种方法
    public function actionAdd()
    {

        SmsMessage::initSms(
            $user,
            [$user->real_name,  date('Y-m-d H:i', $draw->created_at), $draw->money, 'T+1', Yii::$app->params['contact_tel']],
            Yii::$app->params['sms']['tixian_apply']
        )->save(false);

    }
    //数据模型
    public static function initSms(User $user, array $message, $template_id, $level = self::LEVEL_MIDDLE)
    {
        $smsmsg = new self([
            'uid' => $user->id,
            'template_id' => $template_id,
            'mobile' => $user->mobile,
            'level' => $level,
            'message' => json_encode($message),
        ]);

        return $smsmsg;
    }



}
```