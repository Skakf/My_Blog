#  shell bash 基本小常识

1. set

    命令用来修改 Shell 环境的运行参数，也就是可以定制环境。一共有十几个参数可以定制，[官方手册](https://link.zhihu.com/?target=https%3A//www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html)有完整清单，本文介绍其中最常用的四个。 

    命令行下不带任何参数，直接运行`set`，会显示所有的环境变量和 Shell 函数 

   1）set -u

   一般执行脚本时如果遇到不存在的变量，会忽视他。

   但脚本在头部加上它，遇到不存在的变量就会报错，并停止执行。 

   2）set -x

   一般情况下，脚本会连续执行，分不清结果对应于哪条执行语句

   -x 会先显示 执行的是什么命令，再显示执行结果 （调试方便）

   3）command || exit 1

    如果脚本里面有运行失败的命令（返回值非0），Bash 默认会继续执行后面的命令 ，但这种不利于脚本安全，而且会导致错误累计

   command这样表示只要command有非零返回值，脚本就会停止执行

   如果停止执行前，还要有多个操作，可以以下三种写法：

    

   ```sh
   command || {echo "command failed"; exit 1;}
   if !command; then echo "command failed"; exit 1; fi
   command if ["$?" -ne 0]; then echo "command failed"; exit 1; fi
   ```

   另外，如果如果两个命令有继承关系，一个执行成功了才能继续第二个，则要用

   ```sh
   command 1 && command 2
   ```

   4） set -e

   上述写法多少有些麻烦，-e从根本解决这个问题，它**使得脚本只要发生错误，就终止执行。**

   `set -e`根据返回值来判断一个命令是否运行失败。但是，某些命令的非零返回值可能不表示失败，或者开发者希望在命令失败的情况下，脚本继续执行下去。这时可以暂时关闭`set -e`，该命令执行结束后，再重新打开`set -e`。

   ```sh
   set +e 
   command1 
   command2 
   set -e
   ```

   上面代码中，`set +e`表示关闭`-e`选项，`set -e`表示重新打开`-e`选项。

   还有一种 

   ```sh
   command || true
   ```

   可以让命令即使失败脚本也不会终止执行

   5）set -o pipefail

   只要一个子命令失败，管道命令就失败，脚本就终止

总结：

set这几个参数一般放一起使用

```sh
set -euxo pipefail
set -eux set -o pipefail
```

写在bash脚本头部



参考：

- https://zhuanlan.zhihu.com/p/44177788 

- [The Set Builtin](https://link.zhihu.com/?target=https%3A//www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html)
- [Safer bash scripts with 'set -euxo pipefail'](https://link.zhihu.com/?target=https%3A//vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/)
- [Writing Robust Bash Shell Scripts](https://link.zhihu.com/?target=http%3A//www.davidpashley.com/articles/writing-robust-shell-scripts/)