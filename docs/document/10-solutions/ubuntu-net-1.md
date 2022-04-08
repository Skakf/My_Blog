---
title: ubuntu-配置网络
date: 2019-08-01 10:06:38
tags:网络
---

每年都要经历配网的艰辛历程，这次我一定要记录下来！
（虽然这次也是瞎配一通，莫名成功

## Problems：

1. ping baidu name or server  no found
2. 没有VMnet0
3. 桥接NAT都不行


## Solution：
1. 配置静态ip
 
1) vim /etc/network/interfaces


```
 auto ens33  
	iface ens33 inet staticaddress 192.168.112.93
	network 192.168.112.0
	netmask 255.255.255.0
    gateway 192.168.112.2
	dns-nameserver 114.114.114.114

```
tips：要根据自己现有网关修改112处具体值

可通过vmware-编辑-虚拟网络编辑器-NAT 查看

（神奇的是乱搞一通后br0没有了？）

2) vim /etc/systemd/resolved.conf

加入

```

[Resolve]

DNS=192.168.112.2

\#xxx

\#xxx
```

--------------以上两步联合-----------------

方法2

好像Ubuntu18.04 换了配网方式？

netplan

参考：https://blog.csdn.net/qq_42975842/article/details/81705244



2.管理员权限启动vmware 就会有vmnet0

也可能是我昨天还安装了一些东西吧
参考：https://blog.csdn.net/diweikang/article/details/78643049



（麻袋窝还是要吐槽一下，感觉markdown并没有让我的记录更简洁一些呀？！每次排版要花好久，强迫症超气的！（我知道是我不会用=-= 略）
 


