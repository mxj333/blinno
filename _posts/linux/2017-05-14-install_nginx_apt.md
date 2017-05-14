---
layout: default
title: install nginx apt
meta: For Debian/Ubuntu, in order to authenticate the nginx repository signature and to eliminate warnings about missing PGP key during installation of the nginx package.
source: http://www.blinno.net
category: linux
---
<h2>{{ page.title }}</h2>
<p>{{ page.date | date: "%Y-%m-%d" }}</p>


1、
For Debian/Ubuntu, in order to authenticate the nginx repository signature and to eliminate warnings about missing PGP key during installation of the nginx package, it is necessary to add the key used to sign the nginx packages and repository to the apt program keyring. Please download this key from our web site, and add it to the apt program keyring with the following command:
```
wget http://nginx.org/keys/nginx_signing.key

sudo apt-key add nginx_signing.key
```
2、
For Ubuntu replace codename with Ubuntu distribution codename, and append the following to the end of the /etc/apt/sources.list file:

```
sudo apt-get install vim 
sudo vim /etc/apt/sources.list
```
在文件最后加上以下内容：
```
deb http://nginx.org/packages/ubuntu/ trusty nginx
deb-src http://nginx.org/packages/ubuntu/ trusty nginx
```

3、
For Debian/Ubuntu then run the following commands:
```
apt-get update
apt-get install nginx
```

4、If you do not have a domain name pointed at your server and you do not know your server's public IP address, you can find it by typing one of the following into your terminal:
```
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```
OR
```
curl http://icanhazip.com
```
====================================

install mysql
```
sudo apt-get install mysql-server mysql-client -y 
```
First, we need to tell MySQL to generate the directory structure it needs to store its databases and information. We can do this by typing:
首先，我们需要告诉MySQL来产生，它需要存储它的数据库和信息的目录结构。我们可以通过键入做到这一点
```
sudo mysql_install_db
```
Next, you'll want to run a simple security script that will prompt you to modify some insecure defaults. Begin the script by typing:
接下来，你要运行一个简单的安全脚本，会提示你修改了一些不安全的默认值。首先输入脚本：
```
sudo mysql_secure_installation
```
======================================
Install PHP for Processing
```
sudo apt-get install php5-fpm php5-mysql -y

sudo apt-get install php5-cgi 
sudo apt-get install spawn-fcgi
```
-----------------------------------
启动 FastCGI
```
sudo /usr/bin/spawn-fcgi -a 127.0.0.1 -p 9000 -u www-data -g www-data -f /usr/bin/php5-cgi -P /var/run/fastcgi-php.pid


sudo /usr/bin/spawn-fcgi -a 127.0.0.1 -p 9000 -u nginx -g nginx -f /usr/bin/php5-cgi -P /var/run/fastcgi-php.pid

```
停止FastCGI
```
sudo netstat -antup
```
杀死 php5-cgi 的进程: 
```
16462/php5-cgi

sudo kill 7082
```

-----------------------------------
```
sudo apt-get install php5-curl php5-json php5-tidy php5-dev php5-mcrypt php5-xdebug php5-gd php5-xmlrpc php5-intl php5-xsl  php5-redis -y

```
```
sudo apt-get install php5.6-fpm php5.6-mysql php5.6-curl php5.6-json php5.6-tidy php5.6-dev php5.6-mcrypt php5-xdebug php5.6-gd php5.6-xmlrpc php5.6-intl php5.6-xsl  php5.6-redis php5.6-mbstring -y
```

```
sudo apt-get install php7.0 php7.0-cgi php7.0-fpm php7.0-mysql php7.0-curl php7.0-json php7.0-tidy php7.0-dev php7.0-mcrypt php7.0-xdebug php7.0-gd php7.0-xmlrpc php7.0-intl php7.0-xsl  php7.0-redis php7.0-mbstring -y
```


-----------------------------------
```
sudo  vim /etc/php5/fpm/php.ini
```
We will change both of these conditions by uncommenting the line and setting it to "0" like this:
我们将通过取消注释该行并将其设置为“0”，这样的改变两个条件：

```
cgi.fix_pathinfo=0

sudo service php5-fpm restart
```

***************************
```
sudo vim /etc/nginx/sites-available/default
```


```
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root /usr/share/nginx/html;
    index index.php index.html index.htm;

    server_name localhost;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }

    location ~ \.php$ {
        try_files $uri =404;
        include /etc/nginx/fastcgi_params;
        fastcgi_pass    127.0.0.1:9000;
        fastcgi_index   index.php;
        fastcgi_param SCRIPT_FILENAME /usr/share/nginx/html$fastcgi_script_name;
    }
}

```
```
sudo service nginx restart
```
-------------------------

cakePHP 3.0+ 需要安装 php5-intl 扩展
------------------------------------

如果您正在使用 Ubuntu，請儘量使用像 aptitude 或者 synaptic 套件管理程式，代替人工手動操作的方式從這個網頁下載並安裝套件。

您可以使用以下列表中的任何一個鏡像站，只要在您的 /etc/apt/sources.list 文件中像下面這樣添加一行：
```
deb http://security.ubuntu.com/ubuntu precise-security main universe
```
```
sudo apt-get install php5-intl
```
---------------------------------------------------------

```
server {
    listen   82;
    server_name localhost;
    rewrite 301 localhost$request_uri permanent;

    # root directive should be global
    root   /usr/share/nginx/html/xiaobao/webroot/;
    index  index.php;

    access_log /var/log/nginx/xiaobao.access.log;
    error_log /var/log/nginx/xiaobo.error.log;

    location / {
        try_files $uri /index.php?$args;
    }

    location ~ \.php$ {
        try_files $uri =404;
        include /etc/nginx/fastcgi_params;
        fastcgi_pass    127.0.0.1:9000;
        fastcgi_index   index.php;
        fastcgi_param SCRIPT_FILENAME /usr/share/nginx/html/xiaobo$fastcgi_script_name;
    }
}

```

```
server {
    listen   80;
    server_name localhost;

    # root directive should be global
    root   /usr/share/nginx/html/;
    index  index.php index.html index.htm;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    location / {
        try_files $uri /index.php?$args;
    }

    location ~ \.php$ {
        try_files $uri =404;
        include /etc/nginx/fastcgi_params;
        fastcgi_pass    unix:/run/php/php7.0-fpm.sock;
        fastcgi_index   index.php;
        fastcgi_param SCRIPT_FILENAME /usr/share/nginx/html$fastcgi_script_name;
    }
}

```

-------------------------------------------------
```
sudo ln -s ~/dequanyuan /usr/share/nginx/html/

```

======================================

```
No input file specified. 
```
在ubuntu系统中，使用apt-get install nginx和php-cgi，配置好nginx和php。

在/var/www/nginx-default中放上一份phpinfo.php，使用
http://localhost/phpinfo.info
访问，结果报错，显示 “No input file specified”

现存的各种方案，是让你把nginx站点配置文件中的这一句话hardcode起来的（真想骂人）：
# 即把fastcgi_param指令的SCRIPT_FILENAME项 从动态值改为fixed值，如：
fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
# 修改为
fastcgi_param  SCRIPT_FILENAME  /var/www/nginx-default$fastcgi_script_name;

另外还有一种是修改php.ini中的什么
cgi.fix_pathinfo=1
doc_root=
请注意！！以上这些都是非常不良的改动！

问题原因
导致“No input file specified. ”这个问题的原因，是因为nginx的配置不正确，从而导致CGI获取参数错误。
简单来说，是因为$document_root这个变量尚未定义。

Nginx的配置文件中，fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
是一个写在location块(通常是匹配php)中的指令语句。
而我们也知道，在nginx中，有三级的关系，http  >  server  >  location。
如果要在location块中使用$document_root，需要在上层server块或http块中定义了root指令，这样才能通过继承关系，在location块中用$document_root拿到root的值。而定义在别的location块中的root指令的值，是一个局部变量，其值无法在匹配php的这个location块中被获取。

***********************************************************
因此，解决这个问题的办法，是把"/" location块中的root上提到server。
或者，在php的location块中，重新定义root。
***********************************************************

http://nginxlibrary.com/resolving-no-input-file-specified-error/


如果您正在使用nginx的和PHP，CGI和遵循标准程序来设置它，你可能会经常收到“未指定输入文件”的错误。这个错误基本上都是发生在PHP-CGI守护程序无法找到的.php文件中使用的执行SCRIPT_FILENAME被提供给它的参数。我将讨论有关错误的常见原因，它的解决方案。

错误的路径发送到PHP-CGI守护进程
