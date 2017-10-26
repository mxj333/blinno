---
layout: default
title: 在centos7上从源安装Zabbix的分步教程
meta: 在centos7上从源安装Zabbix的分步教程
source: http://www.blinno.net
category: zabbix
---

<h2>{{ page.title }}</h2>
<p>{{ page.date | date: "%Y-%m-%d" }}</p>


### 4 [从源安装](https://www.zabbix.com/documentation/3.2/manual/installation/install) {#installation_from_sources}

你可以通过源编译安装Zabbix的最新版。

本节是从源安装Zabbix的分步教程。

#### 1 安装Zabbix服务器 {#installing_zabbix_daemons}

##### 1 下载源文件 {#download_the_source_archive}

请前往 [Zabbix download page](http://www.zabbix.com/download.php)下载源归档文件。下载后，通过运行以下命令提取源：

```
$ tar -zxvf zabbix-3.2.0.tar.gz
```

```
    在命令中输入正确的Zabbix版本。它必须与下载的存档的名称相匹配。
```

##### 2 创建用户账户 {#create_user_account}

对于所有Zabbix守护进程，需要一个非特权用户。如果Zabbix守护程序从非特权用户帐户启动，它将作为该用户运行。

但是，如果从“root”帐户启动守护程序，它将切换到“zabbix”用户帐户。因此要在Linux系统上创建此类用户帐户（在其自己的组“zabbix”中），请运行：

```
groupadd zabbix
useradd -g zabbix zabbix
```

Zabbix前端安装不需要单独的用户帐户。

如果Zabbix[服务器](https://www.zabbix.com/documentation/3.2/manual/concepts/server)和Zabbix[代理](https://www.zabbix.com/documentation/3.2/manual/concepts/agent)\( 即 zabbix agent\)在同一台机器上运行，则建议使用不同的用户来运行服务器，而不是运行agent。否则，如果两者都作为同一用户运行，则agent可以访问服务器配置文件，并且Zabbix中的任何管理级别用户可以很容易地检索例如数据库密码。

```
    将Zabbix作为root，bin或任何其他具有特殊权限的帐户运行是一种安全风险。
```

##### 3 创建Zabbix数据库 {#create_zabbix_database}

对于Zabbix[服务器](https://www.zabbix.com/documentation/3.2/manual/concepts/server)和[proxy](https://www.zabbix.com/documentation/3.2/manual/concepts/proxy) 守护程序，以及Zabbix前端，需要一个数据库。它不需要运行Zabbix[代理](https://www.zabbix.com/documentation/3.2/manual/concepts/agent)。

[提供](https://www.zabbix.com/documentation/3.2/manual/appendix/install/db_scripts)的SQL[脚本](https://www.zabbix.com/documentation/3.2/manual/appendix/install/db_scripts)用于创建数据库结构和插入数据。Zabbix代理数据库只需要结构，而Zabbix服务器数据库还需要数据集。

创建Zabbix数据库后，继续执行以下编译Zabbix的步骤。

##### 4 配置源 {#configure_the_sources}

配置Zabbix服务器或代理的源时，必须指定要使用的数据库类型。一次只能使用服务器或代理进程编译一个数据库类型。

要查看所有受支持的配置选项，请在提取的Zabbix源目录中运行：

```
./configure --help
```

要配置Zabbix服务器和代理的源，您可以运行如下：

```
./configure --enable-server --enable-agent --with-mysql --enable-ipv6 --with-net-snmp --with-libcurl --with-libxml2
```

--with-libcurl 配置选项与cURL 7.20.0或更高版本需要SMTP认证，支持自Zabbix 3.0.0。

--with-libcurl 和 --with-libxml2 配置选项是虚拟机监视所必需的，从Zabbix 2.2.0开始支持。

注意  ：

编译源码是最容易发生错误的，常见的就是“ Not found XXX library”，下面是常见情况的解决方法：

1. **  configure: error: MySQL library not found**  
     the problem is not installed mysql-devel

   ```
     apt-get install  libghc6-hsql-mysql-dev
   ```

2. **configure: error: Jabber library not found**  
     the problem is not installed jabber lib

   ```
     apt-get install libphp-jabber  
     apt-get install libnet-jabber-loudmouth-perl  
     apt-get install jabber-dev  
     apt-get install libiksemel-dev  \(\* this packet important\)
   ```

3. **configure: error: Not found curl Library**  
     the problem is not installed libcurl4-openssl-dev

   ```
     apt-get install libcurl4-openssl-dev
   ```

4. **configure: error : Not found NET-SNMP library**

   ```
     apt-get install libsnmp-dev  
     apt-get install snmp
   ```

5. **Invalid OPENIPMI directory - unable to find ipmiif.h**

   ```
     apt-get install libopenipmi-dev
   ```

6. **msgfmt: command not found**

   ```
   apt-get install gettext
   ```

7. **Not found mysqlclient library**

   ```
     sudo apt-get install libmysqld-dev
   ```

8. **LIBXML2 library**

   ```
     sudo apt-get install libxml2-dev 
   ```

9. **Invalid Net-SNMP directory - unable to find net-snmp-config**

   ```
     sudo apt-get install snmpd
   ```

要配置Zabbix服务器（使用PostgreSQL）的源，您可以运行：

```
./configure --enable-server --with-postgresql --with-net-snmp
```

要配置Zabbix代理的源（使用SQLite等），您可以运行：

```
./configure --prefix=/usr --enable-proxy --with-net-snmp --with-sqlite3 --with-ssh2
```

要配置Zabbix代理的源，您可以运行：

```
./configure --enable-agent
```

您可以使用--enable-static标志来静态链接库。

如果计划在不同服务器之间分发已编译的二进制文件，则必须使用此标志使这些二进制文件在不需要库的情况下工作。

请注意，--enable-static[在Solaris下不起作用](http://blogs.sun.com/rie/entry/static_linking_where_did_it)。

在构建服务器时不建议使用--enable-static选项。

为了静态构建服务器，您必须具有所需的每个外部库的静态版本。在配置脚本中没有严格的检查。

如果使用了--enable-agent选项，则编译命令行实用程序zabbix\_get和zabbix\_sender。

添加可选路径到MySQL配置文件 --with-mysql=/&lt;path\_to\_the\_file&gt;/mysql\_config，以便在需要使用不在默认位置的MySQL客户端库时选择所需的MySQL客户端库。

当MySQL安装了多个版本或MariaDB安装在同一系统上的MySQL时，这是有用的。

使用--with-ibm-db2标志来指定CLI API的位置。

使用--with-oracle标志来指定OCI API的位置。

有关加密支持，请参阅[使用加密支持编译Zabbix](https://www.zabbix.com/documentation/3.2/manual/encryption#compiling_zabbix_with_encryption_support)。

#####  {#make_and_install_everything}

##### 5 制作并安装一切 {#make_and_install_everything}

如果从SVN安装，需要先运行：

`$ make dbschema`

```
make install
```

此步骤应以具有足够权限（通常为“root”或通过使用`sudo`）的用户身份运行。

运行`make install`将默认情况下将守护程序二进制文件（zabbix\_server，zabbix\_agentd，zabbix\_proxy）安装在 /usr/local/sbin中，并将客户端二进制文件（zabbix\_get，zabbix\_sender）安装在/usr/local/bin中。

要指定与 /usr/local不同的位置，请在配置源的上一步中使用--prefix，例如 --prefix=/home/zabbix。

在这种情况下，守护程序二进制文件将安装在 &lt;prefix&gt;/sbin下，而实用程序在 &lt;prefix&gt;/bin下安装。

手册页将安装在 &lt;prefix&gt;/share下。

##### 6 查看和编辑配置文件 {#review_and_edit_configuration_files}

* 编辑Zabbix代理配置文件
  **/usr/local/etc/zabbix\_agentd.conf**

您需要为安装了zabbix\_agentd的每台主机配置此文件。

您必须在文件中指定Zabbix服务器**IP地址**。来自其他主机的连接将被拒绝。

* 编辑Zabbix服务器配置文件
  **/usr/local/etc/zabbix\_server.conf**

您必须指定数据库名称，用户和密码（如果使用任何）。

使用SQLite必须指定数据库文件的完整路径，不需要DB用户和密码。

如果您有小型安装（最多十个受监控的主机），其余参数将适合您的默认值。如果想要最大化Zabbix服务器（或代理）的性能，您应该更改默认参数。有关更多详细信息，请参阅[性能调优](https://www.zabbix.com/documentation/3.2/manual/appendix/performance_tuning)部分。

* 如果您已经安装了Zabbix代理，请编辑代理配置文件
  **/usr/local/etc/zabbix\_proxy.conf**

您必须指定服务器IP地址和代理主机名（必须为服务器所知），以及数据库名称，用户和密码（如果使用任何）。

使用SQLite必须指定数据库文件的完整路径；不需要DB用户和密码。

##### 7 启动守护程序 {#start_up_the_daemons}

在服务器端运行zabbix\_server。

```
shell> zabbix_server
```

确保您的系统允许分配36MB（或更多）的共享内存，否则服务器可能无法启动，您将在服务器日志文件中看到“ Cannot allocate shared memory for&lt;type of cache&gt;” \(无法为&lt;缓存类型&gt;分配共享内存\)。这可能发生在FreeBSD，Solaris 8上。

请参见本页底部的[“另请参见”](https://www.zabbix.com/documentation/3.2/manual/installation/install#see_also)一节，了解如何配置共享内存。

在所有受监视的计算机上运行zabbix\_agentd。

```
shell> zabbix_agentd
```

确保您的系统允许分配2MB的共享内存，否则代理可能无法启动，您将在代理日志文件中看到“ Cannot allocate shared memory for collector.\(无法为收集器分配共享内存\)”。这可能发生在Solaris 8上。

如果已安装Zabbix代理，请运行zabbix\_proxy。

```
shell> zabbix_proxy
```

#### 2 安装Zabbix Web界面 {#installing_zabbix_web_interface}

##### 复制PHP文件 {#copying_php_files}

Zabbix前端是用PHP编写的，因此运行它需要PHP支持的webserver。安装是通过简单地将PHP文件从frontends/php 复制到webserverHTML文档目录。

Apache Web服务器的HTML文档目录的常见位置包括：

* /usr/local/apache2/htdocs（从源代码安装Apache时的默认目录）
* /srv/www/htdocs（OpenSUSE，SLES）
* /var/www/html（Fedora，RHEL，CentOS）
* /var/www（Debian，Ubuntu）

建议使用子目录而不是HTML根目录。要创建子目录并将Zabbix前端文件复制到其中，请执行以下命令，替换实际目录：

```
mkdir <htdocs>/zabbix
cd frontends/php
cp -a . <htdocs>/zabbix
```

如果从SVN安装并计划使用除英语以外的任何其他语言，则必须生成翻译文件。为此，请运行：

```
locale/make_mo.sh
```

`msgfmt`从gettext包实用程序是必需的。

此外，要使用除英语以外的任何其他语言，其语言环境应安装在Web服务器上。

有关详细信息，[请参阅](https://www.zabbix.com/documentation/3.2/manual/web_interface/user_profile#see_also)用户配置文件”页面中的[“参见”](https://www.zabbix.com/documentation/3.2/manual/web_interface/user_profile#see_also)部分，了解如何安装。

##### 安装前端 {#installing_frontend}

##### 步骤1 {#step_1}

在浏览器中，打开ZabbixURL：[http://&lt;server\\_ip\\_or\\_name&gt;/zabbix](http://&lt;server\_ip\_or\_name&gt;/zabbix)

您应该看到前端安装向导的第一个屏幕。

[![](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_1.png?w=550&tok=fd0e5e)](https://www.zabbix.com/documentation/3.2/_detail/manual/installation/install_1.png?id=manual%3Ainstallation%3Ainstall)

##### 第2步 {#step_2}

确保满足所有软件先决条件。

[![](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_2.png?w=550&tok=0b5ea8)](https://www.zabbix.com/documentation/3.2/_detail/manual/installation/install_2.png?id=manual%3Ainstallation%3Ainstall)

| 先决条件 | 最小值 | 描述 |
| :--- | :--- | :--- |
| _PHP version \(PHP版本\)_ | 5.4.0 |  |
| _PHP memory\_limit选项_ | 128MB | 在php.ini： memory\_limit = 128M |
| _PHP post\_max\_size选项_ | 16MB | 在php.ini中： post\_max\_size = 16M |
| _PHP upload\_max\_filesize选项_ | 2MB | 在php.ini中： upload\_max\_filesize = 2M |
| _PHP max\_execution\_time选项_ | 300秒（允许值0和-1） | 在php.ini： max\_execution\_time = 300 |
| _PHP max\_input\_time选项_ | 300秒（允许值0和-1） | 在php.ini： max\_input\_time = 300 |
| _PHP session.auto\_start选项_ | 必须禁用 | 在php.ini： session.auto\_start = 0 |
| _数据库支持_ | 之一：IBM DB2，MySQL，Oracle，PostgreSQL，SQLite | 必须安装以下模块之一： ibm\_db2，mysql，oci8，pgsql，sqlite3 |
| _bcmath_ |  | php-bcmath |
| _mbstring_ |  | php-mbstring |
| _PHP mbstring.func\_overload选项_ | 必须禁用 | 在php.ini中： mbstring.func\_overload = 0 |
| _PHP always\_populate\_raw\_post\_data选项_ | 必须禁用 | 仅对PHP 5.6.0或更高版本有效。 在php.ini： always\_populate\_raw\_post\_data = -1 |
| sockets |  | php-net-socket。需要用户脚本支持。 |
| _gd_ | 2.0以上 | php-gd。PHP GD扩展必须支持PNG图像（_--with-png-dir_），JPEG（--_with-jpeg-dir_）和FreeType 2（--_with-freetype-dir_）。 |
| _libxml_ | 2.6.15 | php-xml或php5-dom |
| _xmlwriter_ |  | php-xmlwriter |
| _xmlreader_ |  | php-xmlreader |
| _ctype_ |  | php-ctype |
| session |  | php会话 |
| _gettext_ |  | php-gettext 从Zabbix 2.2.1开始，PHP的gettext扩展不是安装Zabbix的强制性要求。如果没有安装gettext，前端将照常工作，但是，翻译将不可用。 |

可选的先决条件也可以存在于列表中。失败的可选先决条件显示为橙色，并显示_警告_状态。使用失败的可选先决条件，设置可以继续。

如果需要更改Apache用户或用户组，则必须验证对会话文件夹的权限。

否则Zabbix安装可能无法继续。

##### 步骤3 {#step_3}

输入连接到数据库的详细信息。必须已创建Zabbix数据库。

[![](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_3.png?w=550&tok=65e211)](https://www.zabbix.com/documentation/3.2/_detail/manual/installation/install_3.png?id=manual%3Ainstallation%3Ainstall)

##### 步骤4 {#step_4}

输入Zabbix服务器详细信息。

[![](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_4.png?w=550&tok=b0e91d)](https://www.zabbix.com/documentation/3.2/_detail/manual/installation/install_4.png?id=manual%3Ainstallation%3Ainstall)

##### 步骤5 {#step_5}

查看设置摘要。

[![](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_5.png?w=550&tok=91477d)](https://www.zabbix.com/documentation/3.2/_detail/manual/installation/install_5.png?id=manual%3Ainstallation%3Ainstall)

##### 步骤6 {#step_6}

下载配置文件并将其放在conf/下您复制Zabbix PHP文件的webserverHTML文件子目录中。

[![](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_6.png?w=550&tok=4664a0)](https://www.zabbix.com/documentation/3.2/_detail/manual/installation/install_6.png?id=manual%3Ainstallation%3Ainstall)

[![](https://www.zabbix.com/documentation/3.2/_media/manual/installation/saving_zabbix_conf.png?w=350&tok=329cb9)](https://www.zabbix.com/documentation/3.2/_detail/manual/installation/saving_zabbix_conf.png?id=manual%3Ainstallation%3Ainstall)

为web服务器用户提供对conf/目录的写访问，配置文件将被自动保存，并且可以立即进行到下一步。

##### 步骤7 {#step_7}

完成安装。

[![](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_7.png?w=550&tok=0d699c)](https://www.zabbix.com/documentation/3.2/_detail/manual/installation/install_7.png?id=manual%3Ainstallation%3Ainstall)

##### 步骤8 {#step_8}

Zabbix前端准备好了！默认用户名为**Admin**，密码为**zabbix**。

[![](https://www.zabbix.com/documentation/3.2/_media/manual/quickstart/login.png?w=350&tok=128699)](https://www.zabbix.com/documentation/3.2/_detail/manual/quickstart/login.png?id=manual%3Ainstallation%3Ainstall)

继续[开始使用Zabbix](https://www.zabbix.com/documentation/3.2/manual/quickstart/login)。

### 也可以看看 {#see_also}

1. [如何为Zabbix守护程序配置共享内存](http://www.zabbix.org/wiki/How_to/configure_shared_memory)



