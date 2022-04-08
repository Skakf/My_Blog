# AFL 吹响冲锋号角的第一次源码学习，嘟！

## AFL基本用法——测试脚本demo

1. 安装 

   ```shell
   wget http://lcamtuf.coredump.cx/afl/releases/afl-latest.tgz
   tar xvf afl-latest.tgz
   cd afl-2.52b
   sudo make && sudo make install
   在/usr/local/bin下，修改.bashrc 加入环境变量 export PATH=/usr/local/bin:$PATH
   source ~/.bashrc
   ```

2. 自带afl-gcc编译   `afl-gcc afl-demo1.c -o afl-demo1`

3. 准备测试用例作为输入，放在in文件夹

4. 开始fuzz @@ 用于替换输入的参数 `afl -i in -o out ./afl-demo1 @@`

## AFL 源码目录结构

| 文件名                      | 功能                                  |
| --------------------------- | ------------------------------------- |
| afl-as.c,afl-as.h,afl-gcc.c | 普通模式插桩，gcc和clang都适用        |
| llvm_mode                   | llvm模式插桩，仅clang适用             |
| qemu_mode                   | qemu模式插桩，针对二进制文件          |
| afl-fuzz.c                  | fuzzer实现核心代码                    |
| libdislocator               | 简单的内存检测工具                    |
| libtokencap                 | 语法关键字提取并生成字典文件          |
| afl-analyze.c               | 对测试样例的字典进行分析              |
| afl_cmin                    | 对fuzzing用到的语料库进行精简操作     |
| afl-tmin.c                  | 对fuzzing用到的测试用例进行最小化操作 |
| afl-gotcpu.c                | 统计cpu占用率                         |
| afl-plot                    | 绘制报告图标                          |
| afl-showmap.c               | 打印目标程序单轮fuzz后的tuple信息     |
| afl-whatsup                 | 各个并行例程fuzzing结果统计           |
| alloc-inl.h                 | 定义带检测功能的内存分配和释放操作    |
| config.h                    | 定义配置信息                          |
| debug.h                     | 提示信息相关的宏定义                  |
| Hash.h                      | hash函数的实现和定义                  |
| Types.h                     | 部分类型及宏定义                      |
| test-instr.c                | 测试程序                              |
| Docs                        | 项目相关的说明文档                    |
| experimental                | 新特性实验研究                        |



## 代码分析

## afl-analyze.c

main() ——  处理用户输入的参数

## 插桩模块

什么是插桩

在哪个环节

如何插桩

​	afl-as 转换成汇编代码之后插

​	分支处插桩

## 代码分析

插桩，辅以mutated input生成。插桩之后会记录执行过程中的分支信息，

随后fuzzer根据这些判断执行流程和代码覆盖情况。

fork server 

记录状态，管道

共享内存 

AFL使用共享内存，来完成插桩信息在fuzzer和target之间的传递。

---

主要代码：afl-as.c + afl-as.h

afl-as.c  :  实现对程序进行插桩的逻辑，在什么位置插桩

afl-as.h  :  实现插桩代码，插桩时的具体操作

---

### 1. afl-as.c

### 2. afl-as.h

### 3. fork-server 逻辑

fork-server (fork()	 target 	)	fuzzer

### 4. 共享内存



## 变异策略

6种

- bitflip	按位翻转，1-0 0-1

- arithmetic     整数加减算术运算

- interest    把一些特殊内容替换到原文件中

- dictionary    把自动生成或用户提供的token替换插入到源文件中

- havoc    大破坏，对原文件进行随机大量变异

- splice    绞接，两个文件拼接得到新文件

其中前四项是非dumb模式(-d)和主fuzzer(-M)会进行的操作，由于其变异方式没有随机性，也称为deterministic fuzzing；后两个有大量随机变化，是所有状况的fuzzer都会执行的变异

下面具体介绍

### bitflip

拿到一个原始文件，打头阵的就是bitflip，会根据翻转量/步长进行多种不同的翻转，按顺序依次为：

- bitflip 1/1,	每次翻转1个bit，按照每1bit的步长从头开始

- bitflip 2/1，  每次翻转相邻的2个bit，按照每1bit的步长从头开始

- bitflip 4/1，  每次翻转相邻的4个bit，按照每1bit的步长从头开始

- bitflip 8/8，  每次翻转相邻的8个bit，按照每8bit的步长从头开始，即依次对每个byte做翻转

- bitflip 16/8，每次翻转相邻的16个bit，按照每8bit的步长从头开始，即依次对每个word做翻转

- bitflip 32/8，每次翻转相邻的32个bit，按照每8bit的步长从头开始，即依次对每个dword做翻转

  bit			<u>00000000</u>	 <u>00000000</u> 	<u>00000000</u>	 <u>00000000</u>

  byte			  0					0					0				0

  word						0										0

  dword											0

在上述过程中，AFL巧妙的嵌入了一些对文件格式的启发式判断

#### 自动检测token

#### 生成effector map









## 参考文献

 [https://v1ckydxp.github.io/2019/11/09/AFL-fuzz-%E5%85%A5%E9%97%A8&%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/](https://v1ckydxp.github.io/2019/11/09/AFL-fuzz-入门&源码分析/) 

 http://rk700.github.io/2018/01/04/afl-mutations/  变异

 [https://muzibing.github.io/2019/09/07/2019.09.07%EF%BC%8880%EF%BC%89/](https://muzibing.github.io/2019/09/07/2019.09.07（80）/)  插桩