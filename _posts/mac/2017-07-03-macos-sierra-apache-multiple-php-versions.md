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


[](https://getgrav-grav.netdna-ssl.com/user/pages/03.blog/macos-sierra-apache-multiple-php-versions/it-works.png?g-9cf60d2d)
