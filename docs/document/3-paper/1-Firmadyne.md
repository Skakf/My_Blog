## NDSS 2016   Towards Automated  Dynamic Analysis for Linux-based Embedded Firmware 

论文总结：

### 1） 简介（目的，功能，干啥的）

-  Firmadyne，一个针对 “基于linux平台的嵌入式固件”的自动化动态分析，检测 固件 是否存在漏洞 的这么一个东西
-  嵌入式固件：主要是针对有网络连接的 线下实体商品 例如 路由器
-  功能：爬镜像、仿真模拟出来固件(_依赖基于软件的全系统f仿真_)、配置网络环境、漏洞检测（_自动化分析数千个固件的二进制文件_）
-  结果：   测试真实世界的23035个固件镜像，涉及42个供应商，
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

**仿真**：1）NVRAM  libnvram.so

​	至少有52.6%的固件映像使用名为libnvram的共享库访问硬件非易失性内存（NVRAM），因此要持久化设备特定的配置参数？对于路由器和其他网络设备，这包括在基于web的配置界面上显示的设置，其中包括无线网络的设置、网络适配器MAC地址和web界面的访问凭证。

​	因为这外围通常是抽象为一个键值存储，我们开发了一个自定义用户空间库，截取调用NVARAM--related功能，如const char* nvramxxxx,他们分别从用于从NVRAM获取和设置参数。通过修改内核传递给init二进制文件的系统环境，以便通过LD-PreLOAD包含这个库，我们可以确保所有用户空间进程都继承相同的环境，因为他们是init的子进程。文件系统上临时挂载点用作键值存储的根，允许我们在用户空间中实现这个接口，而不必模拟特定于硬件的外围设备。

​	在测试期间遇到的最常见的一个挑战是：固件镜像数据集使用不同的c工具链编译的，其中一些我们无法访问，如V_A，这种多样性对我们的共享库来说是有问题的，因为所有动态加载的elf二进制文件都必须制定编译他们的动态加载程序的路径，以及动态加载依赖项的文件名，这些依赖项根据系统的不同而不同。

最初试图通过静态编译nvram实现来解决这个问题。然而，这些c库运行时不仅使用了不兼容的内置c特性实现，而且他们也不是作为我i之sxxxx

幸运的是，用于linux系统的elf动态加载其支持延迟连接，这允许将外部函数符号的解析延迟到使用时，通常，编译器通过将存根代码，放在过程连接表（PLT）xxxx





​			2）kernel kprobes framework 拦截系统调用   MIPS/ARM

没有使用提取的内核，而是使用我们自己为arm和MIPs架构定制的预构建内核来替换他，

在内核编译过程中，在自定义linux内核模块中实现分析，该模块用于帮助调试和模拟原始系统环境。通过使用内核动态探测（kprobes）框架连接20个系统调用，我们能够拦截改变执行环境的调用。这包括分配MAC地址、创建网桥、重新启动系统和执行程序等操作，所以这些操作都由我们的框架监控，以正确配置模拟的网络环境。这个功能还可以用来提供对漏洞的自动确认，特别是与系统调用中不应该出现的预定义的有害值结合使用。

​	因为一些固件映像期望在启动时挂在某些文件系统，例如/dev或/proc，所以我们使用rdinit内核参数来运行一个自定义脚本，该脚本在执行init之前初始化这些文件系统，此外我们在启动时加载nandsim内核模块，它模拟通过/dev/mtdx访问的内存技术设备（MTD）分区，这些分区在这些嵌入式设备上经常使用。

由于我们对NVRAM的模拟时不稳定的，所以禁止guest重新启动系统，并通过重新启动init进程来模拟这张行为。这个内核模块还模拟特定于供应商或特定于设备的接口，，例如自定义设备节点，procfs条目或非标准IOCTL的接口，方法时使用通用存根返回成功。



3）系统配置

因为我们主要对实现网络功能的固件反省去，所以需要随仿真硬件进行设备特定的更改，理想情况下，所有网络设备都将通过DHCP协议自动配置自己。

系统在最初的学习阶段每个固件60秒，在此阶段，模拟器使用仿真目标平台的默认硬件外围设备进行配置，并使用qemu内置的socket网络后端最多配置四个仿真网络适配器。在此期间，将收集关于预期的网络配置的信息，特别是，跟踪分配给网络接口的ip地址，以及用于聚合多个网络接口的IEEE 802。1d网桥的存在。此外，我们使用IEEE 802.1Q VLANs 检查以太网帧的标记和分离，一些路由器使用该VLANs将无线客户网络与物理网络隔离开

将这些信息反馈到仿真框架中，从而为系统开发更准确的qemu配置。我们在主机上实例化一个network tap设备，与固件（eth0）中与LAN接口对应的一个模拟网络接口相关联，对于使用vlan的固件映像，我们为TAP接口分配响应的VLAN ID，以便与模拟网络接口相关联。

对于使用vlan的固件映像，我们为ｔａｐ接口分配相应的vlan ID，以便于模拟的网络服务成功通信。

接下来，配置tap接口的ip地址与固件分配给模拟接口的ip地址位于同一子网中，最后我们通过发送icmp请求并使用Nmap执行端口扫描来检查网络连接。

​			3）Nmap

​			4）QEMU 修改源码

动态分析：注册为回调函数

Metasploit

6）创新点

参考

https://www.freebuf.com/column/169425.html