---
layout: default
title: MAC命令行下安装和卸载DMG文件
meta: MAC命令行下安装和卸载DMG文件
source: http://www.blinno.net
category: mac
---

<h2>{{ page.title }}</h2>
<p>{{ page.date | date: "%Y-%m-%d" }}</p>


安装
```
hdiutil attach xxx.dmg
```

卸载
```
hdiutil detach /dev/xxx
```