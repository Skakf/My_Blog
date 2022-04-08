# python智障小课堂：

## 1. split() 和 strip()

split () ——把字符串按某个字符进行分割

strip ()——删除字符串中的特定字符 (\n \r \t ）

key = items[2].split('/')[-1].strip()[:-1].strip()

- split('/'）用/分割

- [-1] 字符串逆序第一个

e.g.

> str = "aaa/bbb/ccc\ntest/ceshi\n"
> print str.split('/')[-1]

> ceshi

- strip()[:-1] 会删掉字符串最后一个字符

因为 log  里 miss : /path/xxxx!，所以逆序且删除最后一个字符，能提取到所需要的值

---

## 2. f.writelines()

f.open( '/path/xx.txt', w+ )     w+  (可读可写，且能覆盖源文件内容)

f.writelines( lines )              会将列表中的字符串写入文件中，但不会自动换行，如果需要换行，要自己再添加 \n 

f.close()

用print 会把内容输出到运行窗口屏幕上，不会写入文件, 所以不能用print

readline（）

---

## 3. 编译报错

今天在编译一个Python程序的时候，一直出现“Non-ASCII character 'xe5' in file”报错问题 

 SyntaxError: Non-ASCII character '\xe5' in file kNN.py on line 24, but no encoding declared;  

出现问题的原因：

Python默认是以ASCII作为编码方式的，如果在自己的Python源码中包含了中文（或者其他非英语系的语言），此时即使你把自己编写的Python源文件以UTF-8格式保存了，但实际上，这依然是不行的。

解决办法很简单，只要在文件开头加入下面代码就行了

#-*- coding: UTF-8 -*-


特别注意：上面这条语句一定要加在源代码的第一行！！！！

默认的python文件是采用ascii编码的，在头部加入# -*- coding: utf-8 -*-   则指定文件的编码格式是utf-8，那么就是说文件内你可以用中文或其他的文字了。

————————————————

原文链接：https://blog.csdn.net/geekmanong/java/article/details/50514984

---

## 4 . python 字符串模糊匹配 fuzzywuzzy

非完全匹配/对位置敏感：fuzz.ratio("fuzzy wuzzy was a bear", "wuzzy fuzzy was a bear")       91

忽略匹配顺序：fuzz.token_sort_ratio("fuzzy wuzzy was a bear", "wuzzy fuzzy was a bear")   100

## 5 difflib

---

 https://www.runoob.com/python3/python3-basic-syntax.html 

## 系统一周集训

## 入门基础

- print
- 注释 # ''' '''  """ """
- if 缩进
- 字符串 print(str[0:-1])  print(str*2)
- print(x, end="")  //不换行输出
- 导入模块 import

- type(a) 查询类型 
- isinstance(a,int)

- 数值的除法包含两个运算符：**/** 返回一个浮点数，**//** 返回一个整数。
- 在混合计算时，Python会把整型转换成为浮点数。

## 标准数据类型

Python3 中有六个标准的数据类型：

- Number（数字）
- String（字符串）
- List（列表）
- Tuple（元组）
- Set（集合）
- Dictionary（字典）

Python3 的六个标准数据类型中：

- **不可变数据（3 个）：**Number（数字）、String（字符串）、Tuple（元组）；

- **可变数据（3 个）：**List（列表）、Dictionary（字典）、Set（集合）。

  ```python
  str="abcde"
  list=['abc',123,2.5,'hahaha',333]
  >print(str[1:3])
  >bc
  >print(list[1:3])
  >[123, 2.5]
  >print(str[2:5])
  >cde '''
  ```

  ## List

  - 改变list元素  
    - a[0] = 9           a[2:5]=[1,2,3]
    - 截取步长  letters[1:4:2]   要分清下标，第几个，步长 的区别  步长=-1 表示逆向读取翻转（a[-1::-1]）a[-1::-2] 逆向隔一个输出  12345 -1：0  5432

  ## Tuple 

不能修改

## Set

a={'abcddd'}  会自动去掉重复的

并集 A+B，

交集A，B共有的，

差集 只在A有的，排除AB共有的   A-（AB交集）

## Dictionary（字典）

字典（dictionary）是Python中另一个非常有用的内置数据类型。

列表是有序的对象集合，字典是无序的对象集合。两者之间的区别在于：字典当中的元素是通过键来存取的，而不是通过偏移存取。

字典是一种映射类型，字典用 **{ }** 标识，它是一个无序的 **键(key) : 值(value)** 的集合。

键(key)必须使用不可变类型。

在同一个字典中，键(key)必须是唯一的。

 创建空字典使用 **{ }** 

```python
 {x: x**2 for x in (2, 4, 6)}   {2: 4, 4: 16, 6: 36} 
tinydict = {'name': 'runoob','code':1, 'site': 'www.runoob.com'}
dict = {}
dict['one'] = "1 - 菜鸟教程"
dict[2]     = "2 - 菜鸟工具"
```

 截取字符串中的一部分，遵循**左闭右开**原则，str[0:2] 是不包含第 3 个字符的。 

“”“ 三引号 保持字符串跨行格式



### 字典键的特性

 1）不允许同一个键出现两次。创建时如果同一个键被赋值两次，后一个值会被记住 

 2）键必须不可变，所以可以用数字，字符串或元组充当，而用列表就不行 

| 元组            | 列表           | 字典                                | 集合                                     |
| --------------- | -------------- | ----------------------------------- | ---------------------------------------- |
| tup=(1,2,3,4,5) | list=[1,2,3,4] | dict={},dict={'name':'ming','id':1} | set={'app','ora','ban'}  a=set('adadad') |
|                 |                |                                     |                                          |
|                 |                |                                     |                                          |
|                 |                |                                     |                                          |

---

迭代器 yield 相当于断点，暂停，之后继续从这里往下执行？



---

## 正则表达：

### 正则表达式修饰符 - 可选标志

正则表达式可以包含一些可选标志修饰符来控制匹配的模式。修饰符被指定为一个可选的标志。多个标志可以通过按位 OR(|) 它们来指定。如 re.I | re.M 被设置成 I 和 M 标志：

| 修饰符 | 描述                                                         |
| :----- | :----------------------------------------------------------- |
| re.I   | 使匹配对大小写不敏感                                         |
| re.L   | 做本地化识别（locale-aware）匹配                             |
| re.M   | 多行匹配，影响 ^ 和 $                                        |
| re.S   | 使 . 匹配包括换行在内的所有字符                              |
| re.U   | 根据Unicode字符集解析字符。这个标志影响 \w, \W, \b, \B.      |
| re.X   | 该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解。 |

start()  span()  end()

 re.match只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回None；而re.search匹配整个字符串，直到找到一个匹配。 

---

# filter()用法

filter(函数，序列)函数用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。

 该函数接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给函数进行判断，然后返回 True 或 False，最后将返回 True 的元素放到新列表中。 

 lambda用法。
Python使用lambda来创建匿名函数。

## lambda

lambda只是一个表达式，函数体比def简单很多。
lambda的主体是一个表达式，而不是一个代码块。仅仅能在lambda表达式中封装有限的逻辑进去。
lambda函数拥有自己的命名空间，且不能访问自有参数列表之外或全局命名空间里的参数。
func = lambda x: x+2

func(1) = 3

文链接：https://blog.csdn.net/program_developer/java/article/details/82024468

# Python startswith()方法

 startswith() 方法用于检查字符串是否是以指定子字符串开头，如果是则返回 True，否则返回 False。如果参数 beg 和 end 指定值，则在指定范围内检查。 

```python
str.startswith(str, beg=0,end=len(string));
```

- str -- 检测的字符串。
- strbeg -- 可选参数用于设置字符串检测的起始位置。
- strend -- 可选参数用于设置字符串检测的结束位置。

## groups（）

原来group的意思是你的正则表达式是由好多组组成的，然后用字符串去匹配这个表达式，group(1)指的是匹配到了正则表达式第一组的子串是什么，group(2)是指匹配到了正则表达式第二组的子串是什么， groups()就是由所有子串组成的集合。比如下面的例子



# [Python使用struct处理二进制](https://www.cnblogs.com/gala/archive/2011/09/22/2184801.html)

有的时候需要用python处理二进制数据，比如，存取文件，socket操作时.这时候，可以使用python的struct模块来完成.可以用 struct来处理c语言中的结构体.
 

struct模块中最重要的三个函数是pack(), unpack(), calcsize()

pack(fmt, v1, v2, ...)   按照给定的格式(fmt)，把数据封装成字符串(实际上是类似于c结构体的字节流)

**示例一：**

比如有一个结构体

struct Header

{

  unsigned short id;

  char[4] tag;

  unsigned int version;

  unsigned int count;

}

通过socket.recv接收到了一个上面的结构体数据，存在字符串s中，现在需要把它解析出来，可以使用unpack()函数.

import struct

id, tag, version, count = struct.unpack("!H4s2I", s)

上面的格式字符串中，!表示我们要使用网络字节顺序解析，因为我们的数据是从网络中接收到的，在网络上传送的时候它是网络字节顺序的.后面的H表示 一个unsigned short的id,4s表示4字节长的字符串，2I表示有两个unsigned int类型的数据.


就通过一个unpack，现在id, tag, version, count里已经保存好我们的信息了.

同样，也可以很方便的把本地数据再pack成struct格式.

ss = struct.pack("!H4s2I", id, tag, version, count);

pack函数就把id, tag, version, count按照指定的格式转换成了结构体Header，ss现在是一个字符串(实际上是类似于c结构体的字节流)，可以通过 socket.send(ss)把这个字符串发送出去.

 https://www.cnblogs.com/gala/archive/2011/09/22/2184801.html 



---

# socket 编程下 inet_ntoa() 函数

函数声明：char *inet_ntoa (struct in_addr);

　　返回点分十进制的字符串在静态内存中的指针。

　　所在头文件：<arpa/inet.h>

　　//end linux 下

　　　**函数功能：**将[网络地址转换](http://baike.baidu.com/view/875777.htm)成“.”点隔的字符串格式

---

