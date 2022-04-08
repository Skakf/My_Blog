# openvswitch搭建 br0

按照教程

真心可以

https://blog.csdn.net/x_i_y_u_e/article/details/55668867

我诚心感谢这位兄弟

我的电脑崩的不明不白，又好的不清不楚

主要问题：

1. 我是全新ubuntu18.04  内核 4.15

缺少好多依赖包，所以只能一步步安装，缺啥报错补啥

挂网卡那里卡了好久 br0不能联网  结果好像只是dns的问题

ping baidu不可以，ping ip OK 

需要改 interface 文件 / resolv.conf  nameservers 解决 name or service no found

   2 . KVM 无法启动虚拟机 怀疑是少了什么奇怪的组件 

---

openvswitch

每次开机不知道是不是要重新启动一些服务

不过他疯狂报错，我也是mmp

1. 重启服务

   sudo ovsdb-server --remote=punix:/usr/local/var/run/openvswitch/db.sock \
       --remote=db:Open_vSwitch,Open_vSwitch,manager_options \
       --private-key=db:Open_vSwitch,SSL,private_key \
       --certificate=db:Open_vSwitch,SSL,certificate \
       --bootstrap-ca-cert=db:Open_vSwitch,SSL,ca_cert \
       --pidfile --detach --log-file
   sudo ovs-vsctl --no-wait init
   sudo ovs-vswitchd --pidfile --detach --log-file

2. 因为我新装了无线网卡，所以enp2s0网卡改名字了

   ovs-vsctl del-port br0 enp2s0

   ovs-vsctl add-port br0 enp3s0

   改interfaces文件

   ifconfig enp2 0

   ifconfig br0 192.xxxx up

   sudo route add default gw 192.168.120.2 dev br0

   sudo aptitude purge ebtables

3. 能ping通ip，ping不通baidu

   ubuntu18这个小垃圾改了/etc/resolv.conf,每次开机会自动恢复

   所以长期有效的是：

   sudo vim /etc/systemd/resolved.conf

   ​	DNS=8.8.8.8

   sudo systemctl restart systemd-resolved.service

   此时，ping 14.215.177.39  ping baidu.com 都可以



---

tap0 会被连到 br0  br0又连着eth0

每次kvm不成功，都要删除一下

ovs-vsctl show

ovs-vsctl list-port br0

ovs-vsctl del-port br0

我就是我，原理不通，照搬复制贼强的一把好手