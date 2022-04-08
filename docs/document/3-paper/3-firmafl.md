## QEMU 架构

 QEMU 支持两种操作模式：用户模式仿真和系统模式仿真。*用户模式仿真* 允许一个 CPU 构建的进程在另一个 CPU 上执行（执行主机 CPU 指令的动态翻译并相应地转换 Linux 系统调用）。*系统模式仿真* 允许对整个系统进行仿真，包括处理器和配套的外围设备。 

 qemu实现了两种模式的仿真：qemu系统仿真和qemu用户程序仿真。简单的理解，qemu系统仿真可以理解为仿真了一台PC，可以在上面安装OS，qemu用户程序仿真仅仅是对CPU的仿真，即可以运行不同体系结构的应用程序，可以理解为仿真了应用程序的运行环境。 



1 qemu概述

  qemu是一种快速的多体系结构仿真器，通过动态翻译的技术达到了优异的仿真速度。目前，qemu支持两种操作模式：

- 全系统仿真模式。在这种模式下，qemu完整的仿真目标平台，此时，qemu就相当于一台完整的pc机，例如包括一个或多个处理器以及各种外围设备。这种模式可以用来运行不同的操作系统或调试操作系统的代码。
- 用户态仿真模式。在这种模式下，qemu能够运行不同于主机平台的其他平台的程序（比如，在x86平台上运行为arm平台编译的程序），其中典型的代表wine windows API emulator。另外，在这种模式下能够进行方便的交叉编译和调试。

  对于全系统仿真模式，qemu目前可以支持的硬件列表如下：

- x86 or x86_64 体系结构处理器
- ISA PC （没有PCI总线的PC）
- PowerPC 处理器
- 32/64bit的SPARC 处理器
- 32/64bit的MIPS处理器
- ARM体系结构的处理器
- PXA 270、PXA 255
- OMAP 310、OMAP 2420、OMAP 310

  对于用户态仿真模式，qemu支持的硬件列表如下：x86 (32 and 64 bit), PowerPC (32 and 64 bit), ARM, MIPS (32 bit only), Sparc (32 and 64 bit), Alpha, ColdFire(m68k), CRISv32 and MicroBlaze CPUs are supported.



众所周知，Bochs 是一款可移植的IA-32仿真器，它利用模拟的技术来仿真目标系统，具体来说，将是将目标系统的指令分解，然后模拟分解后的指令以达到同样的效果。这种方法将每一条目标指令分解成多条主机系统的指令，很明显会大大降低仿真的速度。

  qemu则是采用动态翻译的技术，先将目标代码翻译成一系列等价的被称为“微操作”（micro-operations）的指令，然后再对这些指令进行拷 贝，修改，连接，最后产生一块本地代码。这些微操作排列复杂，从简单的寄存器转换模拟到整数/浮点数学函数模拟再到load/store操作模拟，其中 load/store操作的模拟需要目标操作系统分页机制的支持。

  qemu对客户代码的翻译是按块进行的，并且翻译后的代码被缓存起来以便将来重用。在没有中断的情况下，翻译后的代码仅仅是被链接到一个全局的链表上，目 的是保证整个控制流保持在目标代码中，当异步的中断产生时，中断处理函数就会遍历串连翻译后代码的全局链表来在主机上执行翻译后的代码，这就保证了控制流 从目标代码跳转到qemu代码。简单概括下：指定某个中断来控制翻译代码的执行，即每当产生这个中断时才会去执行翻译后的代码，没有中断时仅仅只是个翻译 过程而已。这样做的好处就是，代码是是按块翻译，按块执行的，不像Bochs翻译一条指令，马上就执行一条指令。



---

大家好，今天给大家介绍的这篇文章是usenix2019的一片《FirmAFL-通过增强进程仿真 对物联网固件进行 高性能的灰盒测试》

针对物联网设备的网络攻击越来越多，攻击常常利用物联网固件中的软件漏洞，fuzzing是一种有效的软件测试技术 用于漏洞发现 。

这篇文章 主要 讲的是 Firm-AFL, 第一个高性能灰盒模糊测试器，主要解决了针对物联网设备的模糊测试面临的两个问题，第一，Firm-AFL利用系统仿真这一技术解决了模糊测试在应用于POSIX模型兼容的硬件设备时的不兼容问题， 第二， Firm-AFL利用增强式进程仿真(augmented process emulation)这一全新技术解决了系统仿真(system mode emulation)带来的性能瓶颈。增强式进程仿真将系统模式仿真(system mode emulation)与用户模式仿真(user mode emulation)的优势创新性的结合在一起， 从而同时提供了系统仿真(system mode emulation)带来的高兼容性和用户态仿真(user mode emulation)带来的高性能这两大优势。

评估结果表明：1）Firm-AFL功能完备，能够发现物联网程序中的真实漏洞。

2）FirmAFL的性能比基于系统模式仿真的fuzzing 平均高出8.2倍

3)FirmAFL 能够比基于系统模式的fuzzing更快的发现1-day漏洞，且能够发现0-day 漏洞

---

接下来先简单介绍一些背景知识

1）fuzzing

fuzzing是一组向程序提供随机输入的软件测试技术，在发现真实程序的漏洞方面非常有效，特别是AFL，一个覆盖导向的灰盒模胡工具，在学术界和工业界被广泛使用。

根据执行过程中，收集和使用的信息数量，fuzzers可以分为白盒测试，黑盒测试和灰盒测试。

黑盒：将目标程序视为一个黑盒，且不利用任何来自执行的反馈来引导随机输入的生成。

白盒测试：根据对目标程序的深入了解来选择输入。通常是通过程序分析技术来实现的。

灰盒测试：采用轻量级监控技术，（例如代码覆盖率）收集的有限信息来改进测试。

例如AFL：测试目标程序以收集代码覆盖信息，收集的信息用于指导输入的生成，探索新的执行路径的输入将被用作种子来生成新的输入，而不能产生新的覆盖率的输入将被丢弃。这个策略在实践中非常有效。轻量级的仪器允许灰盒测试执行比白盒模糊多数百或千倍的输入。换句话说，吞吐量，对灰盒来说至关重要。

然而物联网固件面临的fuzzingt挑战：

![1592526651640](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592526651640.png)

![1592498513065](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592498513065.png)

![1592498714810](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592498714810.png)



2）QEMU

QEMU是一种基于动态二进制转换的快速处理器仿真器。与按指令解释目标程序的传统模拟器不同，QEMU一次转换几个基本块，更重要的是，他缓存已翻译的块，并使用块链接将他们连接在一起，这允许执行保持在代码缓存内。，大多数情况下，这样可以将翻译的开销降到最低。

除了指令的翻译，下一个最重要的任务是地址空间的翻译。根据执行模式不同，转换的执行方式不同。在系统模式下，qemu实现了一个软件内存管理单元（MMU）来处理内存访问。软件MMU将guestos (GVA)映射到主机虚拟地址（HVA）。这个映射过程对guestOS是透明的，意味着QEMU仍然允许GOS通过页表接口设置（GVA-GPA的映射。并处理页面错误）

​	在内部，为了加快翻译速度，QEMU使用一个软件翻译后备缓冲区（旁路转换缓冲 页表缓冲 ）（TLB）来缓存翻译结果。此外，为了避免在地址转换发生变化时，代码缓存和块链接失效，所有被翻译的块都使用GPA建立索引，并且块链接只在两个基本块位于同一物理页面时执行，GPA-HVA 映射通过线性映射完成 HVA = GPA+offset

与系统模式仿真相反，用户模式模拟中，主机虚拟地址（HVA）被计算为，（GVA+便宜常量，）这种转换比姿容模式仿真快的多

---

![1592502010370](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592502010370.png)

目前的都不合适，就提出本文的框架

table2

![1592527227050](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592527227050.png)

当前存在的瓶颈：



![1592502142517](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592502142517.png)

![1592502161887](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592502161887.png)

---

本文的目标是为物联网固件实现高性能的灰盒测试，所以需要克服两个挑战：兼容性和性能。

兼容性可通过系统模式仿真来解决，xxx

![1592502334997](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592502334997.png)

总之就是结合两种，提出增强进程仿真。

通过增i强进程仿真，目标是保障 透明性和高效性。

![1592502449765](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592502449765.png)

原理：

固件 在系统模式模拟器中启动，包括他待fuzzing的用户及程序，都在系统模式中启动，当待模糊化的程序到达预定的点（主功能入口或接收到网络包）后，进程执行迁移到 用户模式 模拟，以获得更高的执行速度。 

为了最小化迁移成本，在这两种模式之间共享内存状态。 （更具体的说，虚拟机的物理内存为系统模式仿真被分配 ） RAM文件 

系统模式和用户模式以不同的方式访问这个RAM文件，系统模式将RAM视为物理内存，因此通过物理地址访问他，用户模式通过虚拟地址访问 共享内存。 因此，RAM文件 中的物理页面需要按照页面粒度通过虚拟地址映射到用户模式模拟的地址空间。

有了适当的内存映射，进程能够在用户模式仿真中正确执行，直到遇见系统调用，会迁移回系统模式模拟中处理系统调用。当系统调用结束，迁移回用户模式模拟

具体例子:

![1592522816590](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592522816590.png)

在系统模式下启动物联网固件，并进一步启动指定的物联网程序，，使用DECAF提供的虚拟机内省技术（VMI）能够监控执行的物联网程序的执行，并在执行到达预定分岔点时得到通知。

此时，遍历指定进程的页表，收集虚拟到物理页的映射信息，并将其大送到用户模式的模拟段，然后对于虚拟地址到物理地址的每个映射，用户模式仿真端调用mmap建立映射。从此时开始，暂停系统模式仿真中的执行，将CPU状态发送到用户模式模拟，并在那里继续执行

当发生页面错误处理，转回系统模式执行。

![1592504114102](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592504114102.png)

优化：

预加载

![1592522953625](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592522953625.png)

![1592522975417](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592522975417.png)

![1592504226177](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592504226177.png)

AFL过程：

![1592505462171](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592505462171.png)

![1592505474832](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592505474832.png)

![1592505503421](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592505503421.png)

采用了写时复制的轻量级快照机制，在 fork点上，当模糊执行完成时，恢复快照。

![1592523894197](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592523894197.png)

评估：

三套程序：分别用于侧 仿真的正确性和开销，

4个厂商的7个物联网程序，被用来侧灰盒模糊测试的性能

firmadyne数据集，120个 15个1day相关的固件HTTP uPNP服务，被用来测在漏洞发现方面的透明度和有效性



![1592506210095](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592506210095.png)

![1592506294553](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592506294553.png)

![1592506306496](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592506306496.png)

![1592506459541](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1592506459541.png)