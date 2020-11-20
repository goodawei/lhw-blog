---
title: svn 搭建
date: 2016-05-17 21:25:12
tags: svn
categories: svn
---
# ubuntu svn 搭建步骤：

#### :pig: 安装

```bash
sudo apt-get install subversion
```
#### 建立版本库
```bash
sudo mkdir -p /data/repos
sudo svnadmin create /data/repos/client
sudo svnadmin create /data/repos/server
sudo svnadmin create /data/repos/api
```
<!-- more -->

#### 配置文件
```bash
sudo vi authz
[/]
lhw=rw
zhang=rw

sudo vi passwd
[users]
zhang=123456
lhw=123456

sudo vi svnserve.conf
anon-access = none 
auth-access = write
password-db = passwd
realm = /data/repos 
```
#### 启动svn (注意目录为版本仓库的上一级目录)
```bash
sudo svnserve -d -r /data/repos/
```
#### 杀掉svn   
```bash
killall svnserve
```

如果出现：

```php
svn: warning: cannot set LC_CTYPE locale
svn: warning: environment variable LC_CTYPE is UTF-8
svn: warning: please check that your locale name is correct
```

解决： 参考 http://stackoverflow.com/questions/11300633/svn-cannot-set-lc-ctype-locale

```php
sudo apt-get install language-pack-en-base

sudo locale-gen UTF-8
sudo locale-gen en_GB.UTF-8
sudo locale-gen en_US.UTF-8
```
  