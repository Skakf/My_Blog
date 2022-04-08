# Shell智障小课堂

开课啦

## 第一讲

- #! /bin/bash

- 基本参数

  echo 

  $0	shell脚本文件名

  $1	传参时的第一个参数

  $2	传参时第二个参数，以此类推

  $?	上面的一行的命令是否执行正确，一般正确显示0

  $*	 显示全部的参数名称

  $#	显示有几个参数

- **没用小技巧hh - 变颜色**

  - echo -e '\033[32m---------------------------------------\033[0m'

  ​       写在-----------部分的内容会变颜色

  - echo -e '\033[36m---------------------------------------\033[0m' 

    36是另一种颜色

  - echo -e '\033[32m---------------------------------------\033[1m'

    1m——下面全部内容会变颜色

## 第二讲——If

- ```shell
  if	格式
  if （表达式）;then
  	语句1
  else
  	语句2
  fi
  ```

- 注意1：NUM1=100 （定义变量时=左右不要随便加空格）

- 注意2：比较大小用两个（） if ((xx > xx))

- 逻辑运算符解析：

  -f	判断文件是否存在	eg:	if [ -f filename ]

  -d	判断目录是否存在	eg: 	if [ -d dir ]

  -eq	等于	整形比较

  -ne	不等于

  -lt	 小于

  -gt	大于

  -le	小于等于

  -ge	大于等于

  -a	 and 双方都成立

  -o		or单方成立

  -z		空字符串

- 注意3：if [ -d xx ] / if [ ! -d xx]  【】里面两侧有空格

- **没用小技巧2 - 测试脚本运行是否有错**

  /bin/bash -n test.sh

- echo "OK" >> $FILES 

  a >> 向文件追加内容 	/	a > 覆盖内容

  cat $FILES	显示文件内容

- if / elif / else

- 学习用例：

  ```shell
  #1）比较数字大小
  NUM1=100
  NUM2=200
  if(($NUM1 > $NUM2));then
          echo "This $NUM1 greater than  $NUM2"
  else
          echo "This $NUM1 little than $NUM2"
  fi
  #2 判断文件夹/文件是否存在，不存在创建/追加内容
  FILES=test.txt
  if [ ! -f $FILES ];then
          echo "OK" >> $FILES
  else
          cat $FILES
  fi
  #3 成绩划分优良中差，可以判断传入的成绩所在等级，如果为空，可以回显一个Usage，提示应该传入参数
  scores=$1
  if [ -z $scores ];then
          echo "Usage: {$0 60|80.}"
          exit
  fi
  if [[ $scores -gt 85 ]];then
          echo "gt 85, very good";
  elif [[ $scores -gt 75 ]];then
          echo "75-85, good";
  elif [[ $scores -gt 60 ]];then
          echo "pass";                   # 有；
  else
          echo "no pass";
  fi
  ```

  

## 第三讲

- ·date %Y%m%d· 	——	反引号里面会把内容当一个命令来执行，用这个命令可以把文件夹名按日期自动生成

- 学习案例：备份mysql

## 第四讲

- 一键安装LAMP (apache	mysql	php)
- 先定义变量，判断参数，为空打印menu，不然按照参数安装对应的软件
- 安装过程：wget  url/files  / make && make install /  echo success or error 
- 注意：./configure --prefix=$H_PREFIX    表示自定义安装路径 
- “$1”  变量要引起来

httpd —— 2.4.43 报错，不重要，不想搞了，configure _ apr no found  网上有解决办法

mysql 	没有./configure	所以用 cmake

**补充不一定重要的小知识：（（ a > b））-  可用数字运算符  /  [[  a -gt b ]] - 用字符运算符**

## 第五讲 —— For

格式：

```shell
for 变量 in 字符串
do
	语句1
done
```

- **反引号 ——执行``命令  , 和  $( xxx )  效果一致**
- seq   1 15          用于产生某个数到另一个数之间的所有整数        
- expr 1 + 2          求表达式变量的值（注意+前后空格）
- sh -x  sum.sh     -x 可以看脚本执行过程

- **重大发现：ubuntu_for(i=0;i<10;i++)报错：Syntax error: Bad for loop variable**

> 简单概括，就是Ubuntu6.10以后，bash shell被改成了dash shell，bash支持c语言for这种写法，但dash不支持，so，需要更改一下
>
> ```shell
> sudo dpkg -reconfigure dash 	——选择no
> ```
>
> 参考链接： https://blog.csdn.net/liuqinglong_along/article/details/52191382 
>
> 当然，为了确保可移植性，可以选择 `for a in seq 1 10` 

学习案例：

```shell
#案例1：打印seq数字循环
for i in `seq 1 15`							#seq用法：seq 首数 （增量） 尾数
do
        echo -e "\033[32mThe number is $i\033[0m"
done

#案例2：求和1-100
j=0
for ((a=1; a<=10; a++))						##同理可替换为 for a in `seq 1 10`
do
        j=`expr $a + $j`					#expr 
done
echo $j

#案例3：批量压缩和解压缩脚本
##压缩
#!/bin/bash
for i in `find . -maxdepth 1 -name "*.sh"|tail -2`	# find 查找 / . maxdepth 1 当前目录 /
												# tail -2 罗列出所有sh文件取最后两项
												# 小技巧：先在终端检查find是否正确再写入脚本
do
       tar -czvf sh_all.tgz $i					#压缩，此处只能压缩1个文件，涉及覆盖等问题，等后续
done
##解压
for i in `find . -maxdepth 1 -name "*.tgz"`		# 同理，搜索tgz文件
do
        tar -xzvf $i -C ./iftest				#-C 解压到某个指定路径
done

```

没有远程服务器，没有验证过的

```shell
#远程批量拷贝文件
FILES=$*
if [ -z $* ];then
        echo -e "Usage:{$0 /boot|/tmp|//tmp/test.txt}"
for i in `echo 192.168.1.1 127.0.0.1 `
do 
        scp -r /iftest/test.txt root@$i:/root/
        scp -r $FILES
done

#远程主机批量执行命令
for i in `seq 100 200`
do
        ssh -l root 192.168.1.$i `ls /tmp`
done
```

