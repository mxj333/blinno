---
layout: default
title: macOS 10.12 Sierra Web开发环境
meta: macOS 10.12 Sierra Web开发环境
source: http://www.blinno.net
category: mac
---

<h2>{{ page.title }}</h2>
<p>{{ page.date | date: "%Y-%m-%d" }}</p>


第1部分：macOS 10.12 Sierra Web开发环境

这是我们以前的OS X开发系列的更新版本。新发布的macOS 10.12 Sierra需要与以前的版本相比有重大改变，因此需要彻底改版。主要的变化是为什么现在使用Homebrew的Apache，而不是内置的版本，但它应该继续在以前的OS X版本上工作。
在macOS上开发Web应用程序是一个真正的快乐。设置开发环境有很多选择，其中包括不断流行的MAMP Pro，可以在Apache，PHP和MySQL之上提供一个漂亮的UI 。但是，有时候MAMP Pro有减速或过时的版本，或者由于其限制性的配置模板和非标准版本的系统而变得非常糟糕。

人们常常寻找一种替代方法，幸运的是有一种，而且设置相对来说比较简单。

在本博文中，我们将引导您完成并配置Apache 2.4和多个PHP版本。在这篇两篇文章的第二篇博文中，我们将介绍MySQL，Apache虚拟主机，APC缓存和Xdebug安装。

本指南面向有经验的网页开发人员。如果您是初学者开发人员，您将使用MAMP或MAMP Pro更好地服务。
自制安装

这个过程很大程度上依赖于名为Homebrew的macOS包管理器。使用brew命令，您可以轻松地为您的mac添加强大的功能，但首先我们必须安装它。这是一个简单的过程，但你需要启动你的终端（/Applications/Utilities/Terminal）应用程序，然后输入：

ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
只需按照终端提示并在需要时输入密码。这将安装Homebrew并且还安装所需的XCode命令行工具，如果您还没有安装XCode。这可能需要几分钟的时间，但完成后，可以快速确保已安装brew 正确的，只需键入：

$ brew --version
Homebrew 1.0.6
Homebrew/homebrew-core (git revision 1b10; last commit 2016-10-04)
您还应该运行以下命令以确保所有配置都正确：

$ brew doctor
如果您需要纠正任何事情，它会指示您。

额外的冲泡水龙头

我们要使用一些需要一些外部水龙头的酿造：

$ brew tap homebrew/dupes
$ brew tap homebrew/versions
$ brew tap homebrew/php
$ brew tap homebrew/apache
如果您已经安装了Brew，请确保您拥有所有最新的可用冲泡：

$ brew update
现在你准备酿造了！

Apache安装

最新的macOS 10.12 Sierra自带Apache 2.4，但是，由于Apple已经在此版本中删除了一些所需的脚本，所以将此版本与Homebrew一起使用不再是一项简单的任务。但是，解决方案是通过Homebrew安装Apache 2.4，然后将其配置为在标准端口（80/443）上运行。

如果您已经内置了Apache，则需要首先关闭，并且任何自动加载脚本都将被删除。只要按顺序运行所有这些命令，即使是全新的安装，也不会有任何伤害

$ sudo apachectl stop
$ sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null
$ brew install httpd24 --with-privileged-ports --with-http2
这个步骤需要一点时间，因为它从源代码构建Apache。完成后，您将看到如下消息：

🍺  /usr/local/Cellar/httpd24/2.4.23_2: 212 files, 4.4M, built in 1 minute 45 seconds
这很重要，因为您将在下一步中需要该路径。在这个例子中，路径是/usr/local/Cellar/httpd24/2.4.23_2。如果您获得较新版本，只需在下一行中使用该路径：

$ sudo cp -v /usr/local/Cellar/httpd24/2.4.23_2/homebrew.mxcl.httpd24.plist /Library/LaunchDaemons
$ sudo chown -v root:wheel /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist
$ sudo chmod -v 644 /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist
$ sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist
您现在已经安装了Homebrew的Apache，并将其配置为使用特权帐户自动启动。它应该已经运行了，所以你可以尝试通过将浏览器指向您的本地主机来访问您的服务器，您应该看到一个简单的标题，表示“工作！。



疑难解答提示

如果您收到浏览器无法连接到服务器的消息，请先检查以确保服务器已启动。

$ ps -aef | grep httpd
如果Apache启动并运行，您应该会看到几个httpd进程。

尝试重新启动Apache：

$ sudo apachectl -k restart
在重新启动期间，您可以在新的“终端”选项卡/窗口中查看Apache错误日志，以查看是否有任何内容无效或导致问题：

$ tail -f /usr/local/var/log/apache2/error_log
如果这不行，请检查以确保您有 Listen: 80 在你的 /usr/local/etc/apache2/2.4/httpd.conf 配置文件。

Apache通过控制 apachectl 命令所以使用一些有用的命令是：

$ sudo apachectl start
$ sudo apachectl stop
$ sudo apachectl -k restart
该 -k将强制立即重新启动，而不是在apache良好且准备就绪时礼貌地重新启动
Apache配置

现在我们有一个工作的Web服务器，我们将要做的是进行一些配置更改，使其作为本地开发服务器更好地工作。

我们将使用Firs来配置它来更改Apache 的文档根目录。这是Apache从其中提供文件的文件夹。默认情况下，文档根配置为/Library/WebServer/Documents。由于这是一个开发机器，我们假设我们要将文档根目录更改为指向我们自己的主目录中的文件夹。为此，我们需要编辑Apache的配置文件。

/usr/local/etc/apache2/2.4/httpd.conf
为了简单起见，我们将使用内置的TextEditor应用程序进行所有编辑。您可以使用终端从终端启动open -e 命令后跟文件的路径：

$ open -e /usr/local/etc/apache2/2.4/httpd.conf
文本编辑

搜索该词 DocumentRoot，您应该看到以下行：

DocumentRoot "/usr/local/var/www/htdocs"
将其更改为指向您的用户目录 your_user 是您的用户帐户的名称：

DocumentRoot /Users/your_user/Sites
你也需要改变 <Directory>在DocumentRoot行下方的标记引用。这也应该更改为指向您的新文档根：

<Directory /Users/your_user/Sites>
我们删除了目录路径下的可选引号，因为TextEdit可能会尝试将它们转换为智能引号，并且当您尝试重新启动Apache时会导致语法错误。即使您在报价周围编辑并将它们保留在原先的位置，保存文档可能会导致转换并导致错误。
在同一个 <Directory> 阻止你会找到一个 AllowOverride 设置，这应该改变如下：

# AllowOverride controls what directives may be placed in .htaccess files.
# It can be "All", "None", or any combination of the keywords:
#   AllowOverride FileInfo AuthConfig Limit
#
AllowOverride All
此外，我们现在应该启用默认情况下注释掉的mod_rewrite。搜索mod_rewrite.so 通过删除领导来取消注释行 #：

LoadModule rewrite_module libexec/mod_rewrite.so
用户和组

现在我们的Apache配置指向一个 Sites文件夹在我们的主目录。然而，一个问题仍然存在。默认情况下，apache作为用户运行daemon 和组 daemon。这将在尝试访问我们的主目录中的文件时导致权限问题。大约三分之一的下来httpd.conf 文件有两个设置来设置 User 和 GroupApache将运行。更改这些以匹配您的用户帐户（替换your_user 与您的真实用户名），与一组 staff：

User your_user
Group staff
网站文件夹

现在，你需要创建一个 Sites文件夹在您的主目录的根目录。您可以在终端或Finder中进行此操作。在这个新的Sites 文件夹创建一个简单 index.html 并在其中放置一些虚拟内容： <h1>My User Web Root</h1>。

$ mkdir ~/Sites
$ echo "<h1>My User Web Root</h1>" > ~/Sites/index.html
重新启动apache以确保您的配置更改生效：

$ sudo apachectl -k restart
如果在重新启动Apache时收到错误，请尝试删除我们之前设置的DocumentRoot和Directory指定的引号。任何涉及的错误apr_sockaddr_info_get() 可以通过搜索来解决 httpd.conf 文件的行 ServerName localhost 并删除 #。完成此操作后，请尝试重新设置Apache。
指向浏览器 http://localhost应该显示你的新消息。如果你有工作，我们可以继续前进！



PHP安装

我们将继续安装PHP 5.5，PHP 5.6，PHP 7.0和PHP 7.1，并使用一个简单的脚本在我们需要之间切换它们。

您可以通过输入获得可用选项的完整列表 brew options php55， 例如。在这个例子中，我们只是通过Apache支持--with-httpd24 为Apache构建所需的PHP模块。
$ brew install php55 --with-httpd24
$ brew unlink php55
$ brew install php56 --with-httpd24
$ brew unlink php56
$ brew install php70 --with-httpd24
$ brew unlink php70
$ brew install php71 --with-httpd24
这可能需要一些时间，因为您的计算机实际上是从源代码编译PHP。

您必须重新安装每个PHP版本reinstall 命令而不是 install 如果您以前通过Brew安装了PHP。
另外，您可能需要根据需要调整PHP的配置设置。一个常见的事情是改变内存设置，或者date.timezone组态。该php.ini 每个版本的PHP的文件位于以下目录中：

/usr/local/etc/php/5.5/php.ini
/usr/local/etc/php/5.6/php.ini
/usr/local/etc/php/7.0/php.ini
/usr/local/etc/php/7.1/php.ini
PHP 7.1在撰写本文时目前处于发布候选状态，但预计将在2016年底之前发布。您可以安装尽可能少的PHP版本，您可以选择正确吗？
Apache PHP安装程序 - 第1部分

您已经成功安装了PHP版本，但是我们需要告诉Apache使用它们。你将再次需要编辑/usr/local/etc/apache2/2.4/httpd.conf 文件和搜索 #LoadModule php5_module。

你会注意到每个PHP版本都添加了一个 LoadModule但是这些都指向非常具体的版本。我们可以用一些更通用的路径替换这些（精确版本可能不同）：

LoadModule php5_module        /usr/local/Cellar/php55/5.5.38_11/libexec/apache2/libphp5.so
LoadModule php5_module        /usr/local/Cellar/php56/5.6.26_3/libexec/apache2/libphp5.so
LoadModule php7_module        /usr/local/Cellar/php70/7.0.11_5/libexec/apache2/libphp7.so
LoadModule php7_module        /usr/local/Cellar/php71/7.1.0-rc.3_8/libexec/apache2/libphp7.so
修改路径如下：

LoadModule php5_module    /usr/local/opt/php55/libexec/apache2/libphp5.so
LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so
我们一次只能有一个模块处理PHP，所以现在只能注释掉所有的 php56 条目：

#LoadModule php5_module    /usr/local/opt/php55/libexec/apache2/libphp5.so
LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
#LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
#LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so
这将告诉Apache使用PHP 5.6来处理PHP请求。（稍后我们将添加切换PHP版本的功能）。

此外，您必须明确设置PHP的目录索引，因此搜索此块：

<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
并将其替换为：

<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>
保存文件并重新启动Apache，现在我们已经安装了PHP：

$ sudo apachectl -k restart
验证PHP安装

测试PHP是否按预期安装和运行的最佳方法是使用phpinfo（）。这不是你想在生产机器上离开的东西，但在开发环境中是无价的。

只需创建一个名为 info.php 在你的 Sites/您之前创建的文件夹。在该文件中，只需输入行：

<?php phpinfo();
将浏览器指向 http://localhost/info.php 你应该看到一个闪亮的PHP信息页面：



如果您看到类似的phpinfo结果，恭喜！您现在已经有Apache和PHP成功运行了。您可以通过评论来测试其他PHP版本LoadModule ... php56 ...进入和取消注册其中一个。然后只需重新启动apache并重新加载同一页面。

PHP切换器脚本

我们硬编码Apache使用PHP 5.6，但我们真的希望能够在版本之间切换。幸运的是，一些勤奋的人已经为我们做了艰苦的工作，并写了一个非常方便的小PHP切换脚本。

我们会安装 sphp 脚本成为酿造标准 /usr/local/bin：

$ curl -L https://gist.github.com/w00fz/142b6b19750ea6979137b963df959d11/raw > /usr/local/bin/sphp
$ chmod +x /usr/local/bin/sphp
检查你的路径

自制应该增加其首选 /usr/local/bin 和 /usr/local/sbin作为其安装过程的一部分。输入以下内容即可快速测试：

$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
如果没有看到，可能需要手动将其添加到路径中。根据您使用的shell，您可能需要添加此行~/.profile， ~/.bash_profile， 要么 ~/.zshrc。我们假设你使用默认的bash shell，所以添加一行到你的.profile （如果不存在则创建它）文件在用户目录的根目录下：

export PATH=/usr/local/bin:/usr/local/sbin:$PATH
在这一点上，我强烈建议您关闭所有终端标签和窗口。这将意味着开设一个新航站楼继续下一步。这是强烈推荐的，因为一些真正奇怪的路径问题可能会出现与现有的终端（相信我，我已经看到了！）。

Apache PHP安装程序 - 第2部分

虽然我们配置Apache早期使用PHP，但现在我们需要再次更改，以将PHP模块指向PHP切换脚本使用的位置。你将再次需要编辑/usr/local/etc/apache2/2.4/httpd.conf 文件并搜索 LoadModule php 您之前编辑的文字。

您必须替换当前的LoadModule条目（包括任何已注释的）条目：

#LoadModule php5_module    /usr/local/opt/php55/libexec/apache2/libphp5.so
LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
#LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
#LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so
有了这个：

# Brew PHP LoadModule for `sphp` switcher
LoadModule php5_module /usr/local/lib/libphp5.so
#LoadModule php7_module /usr/local/lib/libphp7.so
注释出来的行 php7_module如果您正在安装PHP 7.0或7.1，因为它使用了一个独特的PHP模块处理程序，这是很重要的。该脚本将自动处理取消注释和评论相应的PHP模块。
测试PHP切换

完成这些步骤后，您应该能够使用该命令切换您的PHP版本 sphp 后跟PHP版本的两位数值：

$ sphp 55
您可能必须输入管理员密码，它应该提供一些反馈：

$ sphp 55
PHP version 55 found
Unlinking old binaries...
Linking new binaries...
Linking /usr/local/Cellar/php55/5.5.38_11... 17 symlinks created
Linking new modphp addon...
Fixing LoadModule...
Updating version file...
Restarting homebrew Apache...
Restarting non-root homebrew Apache...
Done.

PHP 5.5.38 (cli) (built: Oct  4 2016 15:48:32)
Copyright (c) 1997-2015 The PHP Group
Zend Engine v2.5.0, Copyright (c) 1998-2015 Zend Technologies
测试你的Apache是​​否现在运行PHP 5.5再次指向您的浏览器 http://localhost/info.php。有一点运气，你应该看到这样的东西：



更新PHP和其他Brew软件包

Brew使它更容易更新PHP和其他安装的软件包。第一步是更新 Brew，以便获取可用更新的列表：

$ brew update
这将排除可用更新列表和任何已删除的公式。要升级包，只需键入：

$ brew upgrade
您将需要切换到每个已安装的PHP版本，并再次运行更新以获取每个PHP版本的更新，并确保运行您打算的PHP版本。
激活特定/最新的PHP版本

由于我们的PHP连接设置的方式，PHP的只有一个版本是挂在一个时间，只有当前活跃的 PHP版本将更新到最新版本。您可以键入以下内容查看当前活动版本：

$ php -v
您可以通过键入以下内容查看具体的PHP版本：

$ brew info php55
homebrew/php/php55: stable 5.5.30 (bottled), HEAD
https://php.net
Conflicts with: php53, php54, php56, php70
/usr/local/Cellar/php55/5.5.28 (498 files, 51M)
  Poured from bottle
/usr/local/Cellar/php55/5.5.29_3 (327 files, 49M)
  Poured from bottle
/usr/local/Cellar/php55/5.5.30 (327 files, 49M)
  Poured from bottle
  ...
您将看到所有版本的PHP 5.5 brew都可用，然后您可以通过键入以下命令切换到特定版本：

$ brew switch php55 5.5.28
好的，这包装了这个3部分系列的第1部分现在，您可以使用快速简便的方式在PHP 5.5,5.6,7.0和7.1之间切换完全功能的Apache 2.4安装。查看第2部分，了解如何使用MySQL，虚拟主机，APC缓存，YAML和Xdebug来设置环境。另请参阅第3部分，了解如何为Apache虚拟主机设置SSL。