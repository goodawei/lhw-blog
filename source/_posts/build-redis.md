---
title: Here are some thing about redis 
date: 2017-09-21 22:50:41
tags: redis
---


```php
# check redis status
ps -aux | grep redis
# list status
redis 950  0.1  0.3  40436  8124 ? Ssl  02:11   0:24 /usr/bin/redis-server 127.0.0.1:6379
vagrant   4688  0.0  0.0  14520  1092 pts/0    S+   06:13   0:00 grep
--color=auto redis
# into server
redis-cli -h 127.0.0.1  -p 6379
# show all key
keys *
# check key's type
type your key
```

```php
use App\Http\Controllers\Controller;
use App\Jobs\ThisIsTestJob;
use Illuminate\Foundation\Bus\DispatchesJobs;

class ThisIsTestBuHenryController extends  Controller
{
    use DispatchesJobs;
    public function fire()
    {
        // reference http://www.cnblogs.com/z1298703836/p/5346728.html
        // If use delay i don't know is there any problem in redis it is zset
        // php artisan queue:work redis : yourself can run this command
        // php artisan queue:work redis --queue=emails : yourself can run this command that only specify queue name
        $this->dispatch((new ThisIsTestJob())->onQueue(('register')));
        //$this->dispatch((new ThisIsTestJob())->delay(60)->onQueue(('emails')));
        //$this->dispatch((new ThisIsTestJob()));
        dd('hello world');
    }
}
```


### Supervisor

```php

[program:artisan]
command                 = /usr/bin/php /home/vagrant/Code/jbh-oauth/artisan queue:work  --sleep=3 --tries=3 --queue=register  --daemon
directory               = /home/vagrant/Code/jbh-oauth
autostart               = true
autorestart             = true
stdout_logfile          = /home/logs/supervisor/supervisor_queue.log
stdout_logfile_maxbytes = 10MB
stderr_logfile          = /home/logs/supervisor/supervisor_queue.log
stderr_logfile_maxbytes = 10MB

# check 

sudo supervisorctl reload

sudo supervisorctl restart artisan

sudo supervisorctl stop artisan

sudo supervisorctl start artisan

sudo supervisorctl status all
```

