1. 拉取一个centos7 镜像
	
	docker pull centos:centos7

2. mac下修改容器映射端口比较麻烦，所以运行容器的时候多预留一些常用端口。

	docker run -it -v /Users/lihongwei/docker/data:/data --name myphp -p 8081:80 -p 33006:3006 9aec5c5fe4ba

3. 运行nginx 容器

	docker run  --name libevent-nginx -p 8080:80 -d c7460dfcab50

```ng
     listen       80;
        server_name  localhost;
        access_log  /logs/nginx/access.log;
        error_log  /logs/nginx/error.log;

      location ~ \.php$ {
            root           /data/www/;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /data/www/$fastcgi_script_name;
            include        fastcgi_params;
      }
```

4. php5.6

-------------------------------

yum install -y libxml2 *openssl* libcurl* libjpeg* libpng* freetype* libzip* libmcrypt*

-------------------------------

yum -y install gcc-c++

tar zxvf cmake-3.6.2.tar.gz

cd cmake-3.6.2

./bootstrap

gmake install

/usr/local/bin/cmake --version

---------------------------------

tar zxvf libzip-1.6.1.tar.gz

cd libzip-1.6.1

cd build/

cmake ..

make

make install

------------------------------------

./configure --prefix=/myphp \
	--with-mysqli \
	--with-iconv-dir \
	--with-freetype-dir \
	--with-jpeg-dir \
	--with-png-dir \
	--with-zlib \
	--with-libxml-dir \
	--enable-simplexml \
	--enable-xml \
	--disable-rpath \
	--enable-bcmath \
	--enable-soap \
	--enable-zip \
	--with-curl \
	--enable-fpm \
	--with-fpm-user=www \
	--with-fpm-group=www \
	--enable-mbstring \
	--enable-sockets \
	--with-gd \
	--with-openssl \
	--with-mhash \
	--enable-opcache \
	--disable-fileinfo

-------------------------------

出现错误： error: off_t undefined; check your library configuration


echo '/usr/local/lib64
/usr/local/lib
/usr/lib
/usr/lib64'>>/etc/ld.so.conf

ldconfig -v


------------------------------------

tar zxvf libevent-2.0.20-stable.tar.gz
cd libevent-2.0.20-stable/
./configure --prefix=/usr/local/libevent-2.0.20-stable/

-------------------------------

cd libevent-0.1.0
./configure --with-php-config=/myphp/bin/php-config --with-libevent=/usr/local/libevent-2.0.20-stable/

-------------------------------

cp /data/php-src/php.ini.devement ./php.ini

vi php.ini 

libevent.so

ps -ef | grep -v grep | grep php-fpm |awk '{print $2}' | xargs kill -9

/myphp/sbin/php-fpm
---------------