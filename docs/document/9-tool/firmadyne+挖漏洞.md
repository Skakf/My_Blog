# 08.05 （一）

1. 较为认真的看了firmadyne那篇论文 回去写总结（md还是没太看懂，至少还讲不清楚）

2. 四篇物联网的论文 看了简介，了解了每篇是关于什么的，但还没细看

计划 明天一天，搞定这五篇文章

周三开始，看看有没有能复现的

---

# 08.06 （二）

1. 看usenix-2019（2）-智能家居平台中物联网设备、物联网云、移动APP之间的关系
	未完（youtube上两个实验攻击家居设备的视频-网不好，之后要看！）
	简单写论文总结

2. firmadyne 开始写论文总结

   ---

   

# 08.07 （三）

## NDSS 2016   Towards Automated  Dynamic Analysis for Linux-based Embedded Firmware 

论文总结：

### 1） 简介（目的，功能，干啥的）

-  Firmadyne，一个针对 “基于linux平台的嵌入式固件”的自动化动态分析，检测 固件 是否存在漏洞 的这么一个东西
- 嵌入式固件：主要是针对有网络连接的 线下实体商品 例如 路由器
- 功能：爬镜像、仿真模拟出来固件(_依赖基于软件的全系统f仿真_)、配置网络环境、漏洞检测（_自动化分析数千个固件的二进制文件_）
- 结果：   测试真实世界的23035个固件镜像，涉及42个供应商，
  	          测试已知漏洞（本系统通过在9486个img上使用74个漏洞利用样本  发现了887个img（来自至少89个不同产品的）易受利用样本的攻击）
  	          检测新的漏洞（ 利用本框架挖到了14个新漏洞，能够影响12个产品的69个img）

### 2） 相关现状
以前有 动态分析 静态分析 但都不如 我们这种系统仿真

### 3） 模块
有四个组件：

 - 爬虫 ： 去供应商网站爬固件镜像

 - 提取固件：binwalk API （custom-written extraction utility） 提取内核（可选）和固件镜像包含的根文件系统

 - 初始仿真：
   ​		根据提取出的文件系统，本系统标识固件镜像的硬件架构 

   ​		然后在QEMU完整系统仿真器的实例中使用预构建的Linux内核，该仿真器匹配目标固件镜像的 体系结构、字节序 和 字宽 （_目前支持三种：little-endian ARM 、little-endian MIPS 、big-endian MIPS_）

   ​		通过拦截文件系统，网络和其它相关内核子系统的系统调用 来进行 初始仿真 以推测系统和网络配置

- 动态分析：此步骤可重复

  按照上一步推断的，动态重新配置 符合固件镜像预期

  本系统旨在实现轻松扩展以便包含新的动态分析和漏洞利用，每个单独的分析结果会包含进数据库 

  三个漏洞检测通道  （发现新漏洞+检测已知漏洞）

### 4） 框架

固件存储库 （存储与每个固件映像对应的二进制文件）

数据库（每个映像相关的信息：提取状态，架构，品牌，文件）

一组虚拟化工作节点 （提取根文件系统和内核）数据库更新当前进度—>提取成功就固件存储库缓存文件系统—>工作节点进入学习模式包括配置和记录网络交互——> 模拟好网络环境后，工作节点分析

编写脚本（智能解析器爬指定供应商的指定驱动程序和二进制文件），也有手工收集的

binwalk API 提取根文件系统

仿真：

1. 提取根文件系统二进制文件的ELF头 识别目标体系结构和字节序，用QEMU完整系统仿真模拟体系结构并用匹配的内核引导
2. 初始仿真时，系统用特殊的学习模式，在我们修改过的内核里记录网络子系统与系统进行的交互
3. 最后，实际仿真阶段，Firmadyne使用匹配的网络环境和仿真的固件交互
4. 动态分析模块进行漏洞检测

### 5） 具体实现

爬虫：Scrapy 框架

提取：binwalk API（修改） 黑名单验证机制 第三方 jefferson & sasquatch 工具 for JFFS2 & SquashFS 文件系统

仿真：NVRAM  libnvram.so

​			kernel kprobes framework 拦截系统调用   MIPS/ARM

​			Nmap

​			QEMU 修改源码

动态分析：注册为回调函数

Metasploit

6）创新点

参考

https://www.freebuf.com/column/169425.html

---

# 08.08  （四）

1. firmadyne论文总结收尾

2. 网上查资料，搜链接，了解固件安全及物联网

   ### 调研结果：https://paper.seebug.org/category/IoT/?page=4  IOT安全的一些链接——知道创宇

   1）摄像头漏洞挖掘 https://paper.seebug.org/649/ 

   > Mongoose](https://github.com/cesanta/mongoose) 是一个嵌入式的 Web 服务器，[gSOAP](https://www.genivia.com/products.html) 是一个跨平台的，用于开发  Web Service 服务端和客户端的工具。[RTSP（Real Time Streaming Protocol）](https://baike.baidu.com/item/RTSP/1276768?fromtitle=RTSP协议&fromid=3361755)，实时流传输协议，是 TCP/IP 协议体系中的一个应用层协议，该协议定义了一对多应用程序如何有效地通过 IP 网络传送多媒体数据。
   >
   > http://www.devttys0.com/2012/11/reverse-engineering-serial-ports/ 如何找到设备上的调试串口
   >
   > `binwalk` 是 devttys0 大神开发的一款固件分析工具，强烈推荐使用 `Github` 上的[教程安装](https://github.com/devttys0/binwalk/blob/master/INSTALL.md)，直接 `apt-get` 安装会缺少很多依赖。
   >
   > 使用 `binwalk` 查看固件结构 binwalk xxxx.bin
   >
   > 固件使用的是 `squashfs` 文件系统，它是一套供 Linux 核心使用的 GPL 开源只读压缩文件系统。所以设备正常运行的时候是不能对固件进行修改的，在前面那部分，我们从串口进去通过修改内核的初始进程的方式进入系统，是由于系统尚未初始化完成，从而获得了对文件系统的读写权限。
   >
   > 在固件的后一部分，包含一个可以写入的区域。一个 `JFFS2` 文件系统，它是在闪存上使用非常广泛的读/写文件系统，设备运行过程中修改过的配置信息和其他数据将被写入这个文件系统中。
   >
   > Shell 是基于 busybox
   >
   > 这个 `busybox` 是静态链接的，不依赖其他的库文件。可以直接利用 `qemu-arm` 模拟运行
   >
   > 
   >
   > 重新打包固件，以便重新刷回到设备   只需要在不改变原始固件结构的前提下，将修改后的文件系统重新打包成固件。具体：中间ffff那种空闲的地方可填充，然后前后再用cat连接起来
   >
   > 
   >
   > 利用编程器将修改后的固件离线刷入固件存储芯片即可

​    2）https://paper.seebug.org/761/    MIDC • 2018 小米IoT安全峰会议题 PPT 公布

https://mp.weixin.qq.com/s/BheJBx6R0jpnMnHqCxG8jA  小米IoT安全峰会—陈洋《小米 IoT 安全思考与实践》 

https://mp.weixin.qq.com/s/5daypoZnAHQiiE637r4dcA  小米IoT安全峰会—胡珀《IoT + AI + 安全 =？》 

https://mp.weixin.qq.com/s/_pzsmZZz9cTcOxIl0cDgGQ  IoT Reverse Engineering 

	> IoT 设计架构里有三个重点，调试接口、固件与存储，都是容易出问题的地方。那比较常见的防护措施有:
	>
	> 1. 固件来讲就是加密与验签
	> 2. 硬件上会有一种叫做一次性存储芯片和eFuses熔断的产品
	> 3. 调试口常见的方式就是禁用
	> 4. SecureBoot 和Flash整体加密比较少使用，可能开发者觉得比较麻烦或需要额外的成本

3） https://paper.seebug.org/690/   KCon 2018 议题解读：智能家居安全——身份劫持  （物联网的交互过程和劫持 逻辑描述 概念 理论 ）

https://paper.seebug.org/657/ 路由器漏洞复现终极奥义——基于 MIPS 的 shellcode 编写 ( 教shellcode怎么写的 写好以后反编译 提取二进制)

https://paper.seebug.org/616/ 智能家居设备的另一种打开方式——如何控制局域网中的小米设备

> 米家app控制小米设备外，小米还提供一种局域网控制的方式，但前提是要获得用于设备认证一串字符串（即token），所以接下来主要介绍如何获取设备token，以及如何实现局域网控制设备。 
>
>  miio获取token  \  通过脚本获取token(下面就以小米智能插座为例，说明如何获取该设备的token  \ 从米家app获取token
>
> \ 从数据库获取token(该方法是读取手机中米家的app中的数据记录来获取设备的token

**https://paper.seebug.org/429/  D-Link系列路由器漏洞挖掘入门(有复现步骤的 )**

此文章针对历史路由器的web漏洞进行分析    路由器的web文件夹 一般就在`suashfs-root/www`或者 `suashfs-root/htdocs`文件夹里。路由器固件所使用的语言一般为 asp,php,cgi,lua 等语言。这里主要进行*php的代码审计*来挖掘漏洞。

3）从模糊测试到漏洞利用--WRT54G无线路由器漏洞挖掘分析实战 

明天有时间 或许可以试一下复现这个

还有CTF那个！！

安装 burpsuite ida  试一下 路由器模拟出来以后 真机能否访问到！！

# 08.09 （五）

1）复现CTF 工控   （完成一半）

binwalk xxx.bin

binwalk -A 385 | more  查看 CPU架构

binwalk 385  查看符号表地址

2）binwalk 用法

binwalk -A   指令系统分析