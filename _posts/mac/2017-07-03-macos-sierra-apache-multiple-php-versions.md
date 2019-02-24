---
layout: default
title: macOS 10.13高Sierra Apache设置：多个PHP版本
meta: 这是我们以前的OS X开发系列的更新版本。新发布的macOS 10.13 High Sierra以及随之而来的Brew更新与以前的发布版本相比需要进行重大更改，因此需要对此过程进行彻底的修改。从10.12开始，我们现在使用Homebrew的Apache，而不是内置的版本，但是这个新的Appraoch更加灵活，并且应该继续在以前的OS X版本上工作。
source: http://www.blinno.net
category: mac
---

<h2>{{ page.title }}</h2>
<p>{{ page.date | date: "%Y-%m-%d" }}</p>


#### 第1部分：macOS 10.13高Sierra Web开发环境

这是我们以前的OS X开发系列的更新版本。新发布的macOS 10.12 Sierra需要与以前的版本相比有重大改变，因此需要彻底改版。主要的变化是为什么现在使用Homebrew的Apache，而不是内置的版本，但它应该继续在以前的OS X版本上工作。


在macOS上开发web应用程序是一个真正的乐趣。有很多选项可用于设置开发环境，包括在Apache，PHP和MySQL之上提供良好UI 的广受欢迎的MAMP Pro。但是，有时候MAMP Pro的速度会变慢，或者版本过时，或者由于配置模板和非标准版本的限制系统而导致性能不佳。

在这篇博文中，我们将引导您设置和配置Apache 2.4和多个PHP版本。

在本系列的第二篇博文中，我们将介绍MySQL，Apache虚拟主机，APC缓存和Xdebug安装。

本指南面向有经验的网页开发人员。如果您是初学者开发人员，您将使用MAMP或MAMP Pro更好地服务。

#### XCode命令行工具

如果您还没有安装XCode，最好先安装命令行工具，因为这些工具将被自制软件使用：
```
xcode-select --install
```
#### Homebrew Install

这个过程在很大程度上依赖于叫做Homebrew的macOS软件包管理器。使用brew命令，您可以轻松地将强大的功能添加到您的Mac，但首先我们必须安装它。这是一个简单的过程，但你需要启动你的终端（/Applications/Utilities/Terminal）应用程序，然后输入：
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

只需按照终端提示，并根据需要输入您的密码。这可能需要几分钟的时间，但完成后，快速确保您已安装brew 正确的，只需输入:
```
brew --version
Homebrew 1.3.6
Homebrew/homebrew-core (git revision 94caa; last commit 2017-10-22)
```

您应该也可以运行以下命令以确保一切正确配置：
```
brew doctor
```
它会告诉你，如果你需要纠正任何事情。

#### Extra Brew Taps
我们将使用需要外接一个PHP扩展：
```
brew tap homebrew/php
```
如果您已经安装了brew，请确保您拥有所有最新的可用的brew：
```
brew update
```
现在你准备好了brew！

#### Apache安装
最新的macOS 10.13 High Sierra预装了Apache 2.4，然而，在Homebrew中使用这个版本已经不再是一个简单的任务，因为苹果已经删除了这个版本中的一些必需的脚本。但是，解决方案是通过Homebrew安装Apache 2.4，然后将其配置为在标准端口（80/443）上运行。

如果您已经有内置的Apache运行，那么需要首先关闭它，然后删除所有自动加载的脚本。按顺序运行所有这些命令真的没什么用处 - 即使是全新的安装：
```
$ sudo apachectl stop
$ sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null
$ brew install httpd
```

如果没有选项，httpd将不需要从源代码构建，因此安装速度非常快。完成后，您应该看到如下信息：
```
/usr/local/Cellar/httpd/2.4.28: 1,622 files, 26.0MB
```

现在我们只需要配置一些东西，以便我们的新Apache服务器自动启动:
```
sudo brew services start httpd
```

您现在已经安装了Homebrew的Apache，并将其配置为使用特权帐户自动启动。它应该已经在运行了，所以你可以尝试通过指向它在浏览器中访问你的服务器http://localhost:8080，你应该看到一个简单的标题，上面写着“It works！” 。


[![](https://getgrav-grav.netdna-ssl.com/user/pages/03.blog/macos-sierra-apache-multiple-php-versions/it-works.png?g-9cf60d2d)]

#### 疑难解答提示
如果您收到浏览器无法连接到服务器的消息，请首先检查以确保服务器已启动。
```
ps -aef | grep httpd
```
如果Apache启动并运行，您应该会看到一些httpd进程。

尝试重新启动Apache：
```
$ sudo apachectl -k restart
```
您可以在重新启动期间在新的“终端”选项卡/窗口中查看Apache错误日志，以查看是否有任何内容无效或导致问题：
```
$ tail -f /usr/local/var/log/httpd/error_log
```

Apache是​​通过控制 apachectl 命令所以一些有用的命令使用是：
```
$ sudo apachectl start
$ sudo apachectl stop
$ sudo apachectl -k restart
```
该 -k将立即强制重新启动，而不是礼貌地重新启动，当Apache是​​好的，准备好了.

#### Apache配置
现在我们有一个可用的Web服务器，我们将要做的就是进行一些配置更改，使其作为本地开发服务器运行得更好。

在最新版本的Brew中，您必须手动将侦听端口设置为默认值 8080 至 80，所以我们需要编辑Apache的配置文件。
```
/usr/local/etc/httpd/httpd.conf
```

为了简单起见，我们将使用内置的TextEditor应用程序来进行所有的编辑。你可以通过使用终端来启动终端open -e 命令，然后是该文件的路径：
```
$ open -e /usr/local/etc/httpd/httpd.conf
```

找到说的那一行
```
Listen 8080
```
并将其更改为 80：
```
Listen 80
```

接下来，我们将其配置为使用更改Apache 的文档根目录。这是Apache看起来提供文件的文件夹。默认情况下，文档根目录被配置为/usr/local/var/www。由于这是一个开发机器，我们假设我们想要将文档根目录指向我们自己的主目录中的一个文件夹。

搜索这个词 DocumentRoot，你应该看到下面这行：
```
DocumentRoot "/usr/local/var/www"
```

将其更改为指向您的用户目录所在的位置 your_user 是您的用户帐户的名称：
```
DocumentRoot /Users/your_user/Sites
```
你也需要改变 <Directory>在DocumentRoot行下方的标记引用。这也应该改成指向你的新文档根目录：
```
<Directory /Users/your_user/Sites>
```
我们删除了目录路径周围的可选引号，因为TextEdit可能会尝试将这些引号转换为智能引号，并且当您尝试重新启动Apache时将导致语法错误。即使您在引号周围进行编辑并将其保留在原来的位置，保存文档可能会导致转换并导致错误。

在这一点上 <Directory> 块你会发现一个 AllowOverride 设置，这应该改变如下：
```
# AllowOverride controls what directives may be placed in .htaccess files.
# It can be "All", "None", or any combination of the keywords:
#   AllowOverride FileInfo AuthConfig Limit
#
AllowOverride All
```

此外，我们现在应该启用默认情况下注释掉的mod_rewrite。搜索mod_rewrite.so 并通过删除领先取消注释行 #：
```
LoadModule rewrite_module lib/httpd/modules/mod_rewrite.so
```

#### 用户和组
现在我们有Apache配置指向一个 Sites我们的主目录中的文件夹。但是，仍然存在一个问题。默认情况下，Apache作为用户运行daemon 和组 daemon。当试图访问我们的主目录中的文件时，这将导致权限问题。大约三分之一的路httpd.conf 文件有两个设置来设置 User 和 GroupApache将在下运行。更改这些以匹配您的用户帐户（替换your_user 与你真正的用户名），与一组 staff：
```
User your_user
Group staff
```

#### 服务器名称
Apache喜欢在配置中有一个服务器名称，但默认情况下这是禁用的，所以搜索：
```
#ServerName www.example.com:8080
```
并将其替换为：
```
ServerName localhost
```

#### 站点文件夹
现在，你需要创建一个 Sites您的主目录的根目录中的文件夹。你可以在你的终端或Finder中做到这一点。在这个新的Sites 文件夹创建简单 index.html 并在其中放入一些虚拟内容，如： <h1>My User Web Root</h1>。
```
$ mkdir ~/Sites
$ echo "<h1>My User Web Root</h1>" > ~/Sites/index.html
```
重新启动Apache以确保您的配置更改已生效：
```
$ sudo apachectl -k restart
```
如果在重新启动Apache时收到错误，请尝试删除我们之前设置的DocumentRoot和Directory指定的引号。

指着你的浏览器 http://localhost 应该显示你的新消息。如果你有这个工作，我们可以继续前进！


#### PHP安装
我们将继续安装PHP 5.6，PHP 7.0，PHP 7.1和PHP 7.2，并使用简单的脚本在需要时切换它们。

您可以通过键入来获得可用选项的完整列表 brew options php56， 例如。在这个例子中，我们只是通过包括Apache支持--with-httpd 为Apache构建所需的PHP模块。
```
brew install php56 --with-httpd
brew unlink php56
brew install php70 --with-httpd
brew unlink php70
brew install php71 --with-httpd
brew unlink php71
brew install php72 --with-httpd
```

这可能需要一些时间，因为您的计算机实际上是从源代码编译PHP。

您必须重新安装每个PHP版本reinstall 命令而不是 install 如果您以前通过Brew安装了PHP。

另外，您可能需要根据需要调整PHP的配置设置。一个常见的事情是改变内存设置，或者date.timezone组态。该php.ini 每个PHP版本的文件位于以下目录中：

```
/usr/local/etc/php/5.6/php.ini
/usr/local/etc/php/7.0/php.ini
/usr/local/etc/php/7.1/php.ini
/usr/local/etc/php/7.2/php.ini
```
你可以安装尽可能多的PHP版本或者尽可能少的PHP版本，你可以选择正确的版本吗？

现在切换回第一个PHP版本：
```
$ brew unlink php72
$ brew link php56
```

#### Apache PHP设置 - 第1部分
您已经成功安装了您的PHP版本，但是我们需要告诉Apache使用它们。你将再次需要编辑/usr/local/etc/httpd/httpd.conf 文件和搜索 #LoadModule php5_module。

您会注意到每个PHP版本都添加了一个 LoadModule入口，但是这些都是指向非常具体的版本。我们可以用一些更一般的路径替换这些路径（确切的版本可能不同）：
````
LoadModule php5_module        /usr/local/Cellar/php56/5.6.31_7/libexec/apache2/libphp5.so
LoadModule php7_module        /usr/local/Cellar/php70/7.0.24_16/libexec/apache2/libphp7.so
LoadModule php7_module        /usr/local/Cellar/php71/7.1.10_21/libexec/apache2/libphp7.so
```
修改路径如下：
```
LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so
LoadModule php7_module    /usr/local/opt/php72/libexec/apache2/libphp7.so
```

我们一次只能有一个模块处理PHP，所以现在除了注释外 php56 条目：
```
LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
#LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
#LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so
#LoadModule php7_module    /usr/local/opt/php72/libexec/apache2/libphp7.so
```
这将告诉Apache使用PHP 5.6来处理PHP请求。（我们将添加以后切换PHP版本的功能）。

你也必须明确地设置PHP的目录索引，所以搜索这个块：
```
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
```
并用此替换它：
```
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>
```
保存文件并停止Apache然后重新启动，现在我们已经安装了PHP：
```
$ sudo apachectl -k stop
$ sudo apachectl start
```

#### 验证PHP安装
测试PHP是否安装并按预期运行的最佳方法是使用phpinfo（）。这不是你想要在生产机器上留下的东西，但它在开发环境中是非常宝贵的。

只需创建一个名为的文件 info.php 在你的 Sites/您之前创建的文件夹。在那个文件中，只需输入一行：
```
<?php phpinfo();
```
将浏览器指向 http://localhost/info.php 你应该看到一个闪亮的PHP信息页面：
