# Qemu

## 基本概念

用户模式仿真（user-mode）: 允许一个 ( Linux ) 进程 执行在不同架构的CPU上，该模式下，QEMU可作为进程级虚拟机

系统用户仿真 (system-mode)： 允许仿真完整的系统， 包括处理器和配套的外设，qemu作为系统虚拟机



主要用途

- 跨平台编译与开发环境
- 虚拟化，尤其是外设仿真
- 安卓仿真器

## TCG 动态二进制翻译

1. 读取源指令流（二进制）：gen_intermediate_code()
2. 解释为微操作：tcg_gen_code()
3. 编译为目标文件（二进制）
4. 动态产生目标指令流



- 将源代码二进制流转换为微操作
- 固定的寄存器映射减少load/store指令
- 翻译块：不包含跳转指令，以分支指令为间隔，翻译块被转换为主机指令的一个单一序列，并放入翻译缓存
  - 缓存中的翻译块用他们客户机的虚拟地址（pc）索引，因此可以快速查到
  - 翻译缓存大小默认为32MB，但是可以自行配制
  - 一旦缓存运行超过空间，全部缓存被清理
- 块链接
- 在主循环中。通过cpu_exec() 进入下一代码块
- 程序执行至无后续连接的代码块，则通过后置代码返回cpu_exec()



## 论文基本概念

CPU是啥意思？？

dynamic translator performs a runtime conversion of the target CPU instructions into the host instruction set



What is interpreter??





micro operations-







# Qemu ppt 小结

# Qemu

## 翻译缓存机制

1. cache translation block 把以前翻译过的代码存起来，节省开销

2. index block with Guest Physical address where the code resides in 索引-客户机物理地址

- GVA - guest virtual addr
- GPA - Guest physical addr

GVA-GPA-TB_cache[GPA] valid?-execute or TB_cache[GPA]=TB



translation block chaining

