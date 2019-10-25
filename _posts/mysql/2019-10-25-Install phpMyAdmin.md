---
layout: default
title: 2019-10-25-Install phpMyAdmin
meta: 
source: http://www.blinno.net
category: mysql
tags: PHP,MYSQL,phpMyAdmin
grammar_cjkRuby: true
---

# Install phpMyAdmin

----------------------------
MySQL實在不太熟的人，可以利用phpMyAdmin來用網頁管理MySQL，直覺的GUI讓作業方便許多:


*************************************************

``` maxima
sudo apt-get install php5 libapache2-mod-php5 -y 
```

*************************************************


 -------------------------------------------------


``` vbscript
sudo apt-get install phpmyadmin -y
```

之後就是問你密碼，還有一些設定，直接Yes下一步。

接著要產生一個phpMyAdmin捷徑給html根目錄

``` groovy
sudo ln -s /usr/share/phpmyadmin /usr/share/nginx/html

sudo ln -s ~/zencms /usr/share/nginx/html

sudo ln -s ~/cake3 /usr/share/nginx/html
```


還有一些安全性設定，之後重啟服務

``` `smali`
sudo php5enmod mcrypt
sudo service php5-fpm restart
```

==========================================================

先确认 php-fpm 是否已启动，默认配置是 127.0.0.1:9000，可以通过 

``` lsl
netstat -nao|grep 9000 
```

查看。
启动 php-fpm 的脚本是：

``` haskell
/data/soft/php/sbin/php-fpm -D 
```

