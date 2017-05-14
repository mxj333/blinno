---
layout: default
title: 在mac上通过brew安装php7
meta: 在mac上安装软件很爽，用brew就搞定了。现在纪录下，用brew安装php7。
source: http://www.blinno.net
category: php
---
<h2>{{ page.title }}</h2>
<p>{{ page.date | date: "%Y-%m-%d" }}</p>


在mac上安装软件很爽，用brew就搞定了。现在纪录下，用brew安装php7。

设置brew
```
 brew tap homebrew/dupes  
 brew tap homebrew/versions  
 brew tap homebrew/homebrew-php 
```

安装php7
```
brew install php70
```
报错了？

configure的时候，报错了：

```
checking for ZLIB support... yes
checking if the location of ZLIB install directory is     defined... no
configure: error: Cannot find libz

```
但是，我查看 libz 其实是有安装的。

```
brew search zlib
homebrew/dupes/zlib ✔   lzlib
```
通过google 和查看 Cannot find libz 发现需要安装 xcode-select

安装 xcode-select
```
xcode-select --install
```
会弹框出来。点击安装就可以了：


安装好了之后。继续
```
brew install php70

```
一段时间之后，安装成功了。

配置文件目录

php.ini
```
/usr/local/etc/php/7.0/php.ini
```

php-fpm.conf
```
/usr/local/etc/php/7.0/php-fpm.conf
```
php, phpize, php-conig
```
ls /usr/local/opt/php70/bin
phar    php  php-cgi  php-config   phpize

```
php-fpm
```
/usr/local/opt/php70/sbin/php-fpm
```
测试

php版本
```
➜ /usr/local/opt/php70/bin/php -v
PHP 7.0.4 (cli) (built: Mar 15 2016 15:40:41) ( NTS )
Copyright (c) 1997-2016 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2016 Zend Technologies 
with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies 
with Xdebug v2.4.0, Copyright (c) 2002-2016, by Derick Rethans

```
php-fpm版本
```
PHP 7.0.4 (fpm-fcgi) (built: Mar 15 2016 15:40:46)
Copyright (c) 1997-2016 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2016 Zend Technologies
    with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies
    with Xdebug v2.4.0, Copyright (c) 2002-2016, by Derick Rethans
```
启动php-fpm

//删掉老的
```
sudo pkill php-fpm
```
//启动新的
```
sbin git:(master) /usr/local/opt/php70/sbin/php-fpm
[15-Mar-2016 16:58:06] NOTICE: [pool www] 'user' directive is ignored when FPM is not running as root
[15-Mar-2016 16:58:06] NOTICE: [pool www] 'group' directive is ignored when FPM is not running as root
[15-Mar-2016 16:58:06] NOTICE: fpm is running, pid 43008
[15-Mar-2016 16:58:06] NOTICE: ready to handle connections
```
打开 index.php 里的 phpinfo()看是否是php7.0.4版本了。

安装扩展

redis
```
brew install php70-redis
```
配置文件：
```
/usr/local/etc/php/7.0/conf.d/ext-redis.ini
```
memcached
```
brew install php70-memcached
```
配置文件：
```
/usr/local/etc/php/7.0/conf.d/ext-memcached.ini
```
swoole
```
brew install php70-swoole
```
配置文件：
```
/usr/local/etc/php/7.0/conf.d/ext-swoole.ini
```
其他扩展类似这样安装。
