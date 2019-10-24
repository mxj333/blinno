---
layout: default
title: Centos7下安装php7扩展pdo_mysql的方法
meta: Centos7下安装php7扩展pdo_mysql的方法.
source: http://www.blinno.net
category: linux
---
<h2>{{ page.title }}</h2>
<p>{{ page.date | date: "%Y-%m-%d" }}</p>


系统之前安装的PHP版本是 ```php71u```，所以我的安装是：

```
yum install php71u-pdo_mysql  -y
```

老规则，安装完成之后重启服务

```
sudo systemctl restart php-fpm
sudo systemctl restart nginx

```