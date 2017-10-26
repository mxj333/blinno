---
layout: default
title: 最小化安装CENTOS7网络配置
meta: 最小化安装CENTOS7网络配置
source: http://www.blinno.net
category: linux
---

<h2>{{ page.title }}</h2>
<p>{{ page.date | date: "%Y-%m-%d" }}</p>


# 最小化安装CENTOS7网络配置

今天最小化安装了 CentOS 722, 启动后发现没有网络，经查阅资料，CentOS7默认情况下启动时没有启动网络。

1、查看IP 

```
ip add


1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens32: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 00:04:5f:9a:e3:1e brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.49/24 brd 192.168.0.255 scope global ens32
       valid_lft forever preferred_lft forever
    inet6 fe80::3298:2cd5:e0dd:e3bb/64 scope link
       valid_lft forever preferred_lft forever
3: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 00:04:5f:9a:e3:1f brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.7/24 brd 192.168.1.255 scope global dynamic ens33
       valid_lft 85293sec preferred_lft 85293sec
    inet6 fe80::40d7:c355:4480:5851/64 scope link
       valid_lft forever preferred_lft forever


```

２、验证网络管理器服务的状态

使用下面的命令来验证网络管理器服务的状态：

```
systemctl status NetworkManager.service -l
```

运行以下命令来检查受网络管理器管理的网络接口：
```
nmcli dev status
```

```
[root@bogon ~]# nmcli dev status
设备   类型      状态    CONNECTION
ens32  ethernet  连接的  ens32
ens33  ethernet  连接的  ens33
lo     loopback  未管理  --
```

如果某个接口的nmcli的输出结果是“已断开”，说明该接口不受网络管理器管理，网络也是没有生效的。
如果某个接口的nmcli的输出结果是“已连接”，这就是说该接口受网络管理器管理。
你可以轻易地为某个特定接口禁用网络管理器，以便你可以自己为它配置一个静态IP地址。

3、修改配置文件
```
        vi /etc/sysconfig/network-scripts/ifcfg-ens33
```        
把ONBOOT=no改为ONBOOT=yes， 改好的内容为：
```

```

4、重启系统或重启网络后生效
```
systemctl restart network.service
```

5、配置静态IP
```
        vi /etc/sysconfig/network-scripts/ifcfg-ens32
```        
把ONBOOT=no改为ONBOOT=yes， 改好的内容为：
```
```

在上图中，“NM_CONTROLLED=no”表示该接口将通过该配置文件进行设置，而不是通过网络管理器进行管理。
“ONBOOT=yes”告诉我们，系统将在启动时开启该接口。

保存修改并使用以下命令来重启网络服务：
```
systemctl restart network.service
```
现在验证接口是否配置正确：
```
ip add
```

最后，重启网络服务。
```
systemctl restart network.service
```
好了，现在一切都搞定了。

6、查看网关和DNS方法

使用traceroute,第一行就是网关
```
traceroute www.baidu.com
```

使用cat /etc/resolv.conf查看DNS
```
cat /etc/resolv.conf
```