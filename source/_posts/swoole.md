---
title: About swoole questions
date: 2017-09-21 23:01:21
tags: swoole
---


```php
    /**
    * fpm only can use Synchronous client
    */
    public function fpm()
    {
        $parms = 'parms';
        $this->tcpClient($parms);// throw a error Swoole\Client is must cli
    }
```

```php
public function serv()
    {
        /**
         * Swoole has three mode pattern maybe refence: https://wiki.swoole.com/wiki/page/353.html
         * have a question at here but i don't know , if not use SWOOLE_BASE there is a error about epoll
         */
        $serv = new \Swoole\Http\Server("0,0,0,0", 9508,SWOOLE_BASE, SWOOLE_SOCK_TCP);

        $serv->on('Request', function($request, $response) {
            $arrParms = [
                'channel'=>  isset($request->get['channel']) ? $request->get['channel'] : '',
                'uid'    =>  isset($request->get['uid']) ? $request->get['uid'] : '',
            ];
            $class = 'BindCard'. constant(__CLASS__ . "::PERM");
            $obj = "App\\Http\\Controllers\\{$class}";

           call_user_func_array(array(new $obj(),'bandCard'), [$arrParms]);

            $response->cookie("User", "Swoole");
            $response->cookie("english_name", "Henry");
            $response->cookie("channel", $arrParms['channel']);
            $response->header("X-Server", "Swoole");

            $json = json_encode([
                'uid'=>$arrParms['uid'],
                'chanle'=>$arrParms['channel'],
            ]);
            $response->end("<h1>Hello Swoole!</h1>".$json);

        });
        $serv->start();
    }
```

```php
class BindCardRepository
{
    public function bandCard($parms)
    {
        $parms +=[
            'data'=>''
        ];
        $this->tcpClient($parms);
    }

    public function tcpClient($parms)
    {
        $client = new \Swoole\Client(SWOOLE_SOCK_TCP, SWOOLE_SOCK_ASYNC);
        //设置事件回调函数
        $client->on("connect", function($cli) use ($parms){
            //log
            $cli->send(serialize($parms));
        });
        $client->on("receive", function($cli, $data){
            // when reveived data do somethings
            print_r(unserialize($data));
        });
        $client->on("error", function($cli){
            echo "Connect failed\n";
        });
        $client->on("close", function($cli){
            echo "Connection close\n";
        });
        //发起网络连接
        $client->connect('192.168.10.10', 9503, 0.5);
    }

}
```

```php
public function tcpServer()
    {
        $serv = new \Swoole\Server("192.168.10.10", 9503,SWOOLE_BASE, SWOOLE_SOCK_TCP);
        $serv->set(array(
            'daemonize'   => false,
            'reactor_num' => 2,
            'worker_num'  => 4
        ));
        $serv->on('connect', function ($serv, $fd){
            echo "This Tcp Server is Connected.\n";
        });
        $serv->on('receive', function ($serv, $fd, $from_id, $data) {
            // do log and do somethings.
            $hanleData = unserialize($data);
            $serv->send($fd, $data);
            $serv->close($fd);
        });
        $serv->on('close', function ($serv, $fd) {
            echo "Tcp Client: Close.\n";
        });
        $serv->start();
    }
```
