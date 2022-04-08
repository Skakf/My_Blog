---
title: Ubuntu14.04下，利用QEMU+gdb调试Linux0.11内核
tags: Linux内核

---

## 步骤：
1. 安装Ubuntu虚拟机
2. 搭建调试环境
3. 结合QEMU、gdb，开始调试

## 具体流程：
1.安装Ubuntu虚拟机
环境：VMware12，Ubuntu14.04

2.搭建调试环境
1)安装必要依赖包
（因为我是新安装的干净Ubuntu，里边还缺少常用软件）

    sudo apt-get update
    sudo apt-get upgrade

(刚开始直接安总是报unable to locate package，应该是需要更新升级一下)

    sudo apt-get install vim cscope exuberant-ctags build-essential qemu lxterminal

2）下载Linux0.11内核源码到本地
https://github.com/yuanxinyu/Linux-0.11

3）解压编译

    cd Downloads
    unzip Linux-0.11-master.zip
    cd Linux-0.11-master
    make
    make start 
![](image/linux-0.11/make-start.PNG>
(弹出qemu界面)

4）调试

    make debug

![](image/linux-0.11/make-debug.PNG>

（再开一个终端）

     gdb tools/system
     target remote localhost:1234

   


     directory ./Linux-0.11-master  //???设置源码目录
     set architecture i8086
     set dissamembly-flavor intel

![1595054059474](\image\linux-0.11\gdb-bootsect.PNG)

然后似乎就可以gdb调试了？
    


     b *0x7c00
     c
     layout split
     x /16b 0x7df0
     b main

3.gdb调试我还不太熟，每步含义都还要再去学一下



（嗯，先酱，明天继续……）

参考链接：
http://blog.chinaunix.net/uid-26299634-id-4943500.html
https://blog.csdn.net/longintchar/article/details/79685055
https://github.com/tinyclub/linux-0.11-lab/blob/master/README.md


小结：
哇，一开始真的是一脸懵啊，都不知道怎么入手，还好好多大佬都调过啦，还留了教程在网上，参考了几篇博客，也用我三脚猫的功夫仿了个大概，（也还不清楚原理==）目前看上去似乎接下来就是gdb调试代码，一步一步跟着走，看内核怎么运行的了，这块也还需要再看看书
然后，听说是gdb版本啊之类的不兼容？所以linux0.11的编译文件啥的有了修改？上边链接里好像是大佬修改好的？等我明天gdb继续调，看别人文档的时候再研究一下
（感觉真的好多不会，好多要学啊==大神弟弟说的果然没错）
加油！


