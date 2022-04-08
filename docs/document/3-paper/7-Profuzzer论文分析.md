# Profuzzer

摘要：

现有的基于变异的模糊器 倾向于 对程序的输入 进行随机变异，而不理解他的底层语法和语义，在本文中，提出一种新的动态测试技术，可以自动恢复和理解在fuzzing过程中对 漏洞发现 至关重要的输入字段，并智能的调整变异策略以提高命中0day的机会。

由于这种探测是透明的与常规模糊连接在一起的，不用提前了解输入规范。

在fuzzing过程，首先对单个字节进行变异，然后自动分析其fuzzing结果。将相关的字节连接在一起，并识别连接他们的字段的类型，这些字节按照特定与类型的策略被进一步突变在一起，这实质上删除了搜索空间，

实验表明：profuzzer 性能显著优于 AFL，AFLfast, VUzzer,Drill,QSYM    10个集中测试项目中发现 42 个 0-day, 30个cve

---

基于生成

基于变异

​	一组合法的输入作为种子，并不断的修改他们，以探索各种执行路径。

类型探测的fuzzing 

通过一种称为探测的轻量级随机模糊过程 自动发现输入字段及其语义，以指导种子突变的在线进化，这种探测完全按依赖于常规的模糊探测，而且动态执行



具体来说，通过对一组种子进行突变，对一个目标程序执行两步fuzzer

 顺字节逐字节探测，每次改变一个字节，针对目标程序枚举他的值，然后移动到下一字节，这个连续的步骤还收集目标在不同字节值下的执行路径信息，在线自动分析这些信息以恢复数据字段，（将相关的字节连接在一起）并确定他们的字段类型，例如其特定内容

贡献：

轻量级探测发现 输入字节和程序行为之间的关系，以指导后续有针对性的种子突变，讲语义知识发现集成到模糊测试，不仅提高准确性，还提高了性能

---

openjpeg 开源的图像编解码器库

AFL： 60% 突变没有帮助 62bytes 只有24bytes有助于覆盖率的提高——根本原因是缺少输入规范的情况下，基于变异的fuzzing 没有得到适当的指导，

AFL-analyze:一个简单的离线文件格式分析器，-推断输入语法 内置的字典构造功能

![1589452144790](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589452144790.png)

Profuzzer:

两级模糊技术

第一阶段：通过每个字节的突变来探测种子输入的输入字段和字段类型，特别是基于目标程序在突变情况下的行为变化（执行路径的改变），可以识别关键的输入字段，这个领域指导在线输入突变，不同的字段类型有不同的优先级，并包含不同的突变策略，例如，我们的方法，避免了对原始数据类型的字段进行突变，

偏移量 + 原始数据

---

架构图

![1589453513888](C:\Users\Lenovo\Desktop\1589453513888.png)

![1589454161643](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589454161643.png)

magic number 发现的例子

![1589454690670](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589454690670.png)

![1589454767528](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589454767528.png)

![1589454274937](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589454274937.png)

openjpeg提供的 bmp 转换组件的简化代码片段 处理每个类型

![1589459562908](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589459562908.png)

6种字段类型：

1）Assertion 该字段只有一个允许程序正确执行的有效值。其他值将导致程序终止并出现错误

![1589459604293](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589459604293.png)

2）raw data

该字段内容不影响程序的执行，

![1589459945796](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589459945796.png)

3）enumerate

由一小组有效值构成，其他值会导致程序错误的终止。

![1589460319483](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589460319483.png)

4）loop Count

决定了在一个循环结构（for while或递归调用）中执行代码片段的次数。。

loop count 对路径计数有很大的影响，但对路径本身的影响可以忽略不计，但是这个图片里有后续影响

![1589461033708](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589461033708.png)

5）Offset

​	确定程序从何处可以访问输入文件的后续数据。偏移量字段对模糊非常重要，因为错误的偏移值可能会导致程序访问范围外的数据（例如文件的末尾）。从而导致在随后的验证检查中出现错误而终止

![1589461870846](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589461870846.png)

6）Size

确定程序从输入文件中读取的数据量，以便进一步处理

![1589462038661](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589462038661.png)

---

1. 类型探测

每次改变一个字节，枚举字节的256个值来收集相应的256个配置执行文件，存在哈希数组，报错各个控制流边缘。edge vector,每个ev被用来提取一些特征，描述了被探测的字节的特征，然后利用这些特性将多个字节分组到一个字段中，并进一步确定字段类型

2. 分组

无效值导致相同的覆盖率和频率度量  最短路径    无效 导致相同的失败路径

最小覆盖相似度值对应于无效突变导致的最短路径，

![1589465216699](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589465216699.png)

3 . 字段类型识别

![1589466526233](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589466526233.png)

offset 0是可以的，从当前字段之后立刻开始访问后续数据，边界外远低于中间值，在边界内，不同偏移量会导致不同的相似度度量，值0的覆盖相似度大于中间值

size： 与offset类似，区别在于0值 覆盖相似度小于中值

---

reprobing

当新的种子输入进行突变，导致执行时间比种子长时，认为重新探测的输入更接近有效输入，因此我们用突变的种子替换当前种子并继续，较长时间便是发生变化的种子通过了更多的验证检查

type-guided mutation

exploration mutation - all value in firld type

exploitation mutation - for special value   potential attack

explore :

1) 不许改raw data

2) enum ：指定 在有效范围内的值，有高概率，在指定一个超过范围的 低概率的值，以探索 遗漏的其他有效值，

3） assertion : 用概率很小的常量来修改他，以防止分类错误（enumerate-assertion）

4）offset： 当其增加或减少x,要在他后面插入或减少x字节，（因为是偏移量）

5）size：当变量增加或减少x,在最后一个非assertion字段前插入或删除x字节，插入数据随机生成，

6）loop: 二分查找来确定他的最大和最小有效值

7）不能分类的用AFL随机突变

exploit：

手工

---

突变效率： profuzzer 开始效率低

![1589468822124](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1589468822124.png)