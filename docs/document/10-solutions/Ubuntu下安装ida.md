# Ubuntu下安装ida

1. 下载wine
2. 把windows下ida6.8安装文件夹拷贝过去
3. wine 目录路径/ida.exe 可打开

报错解决：

1. err:module:import_dll Loading library python27.dll (which is needed by L"Z:\\home\\anciety\\ida\\plugins\\python.plw") failed (error c000007b).

   https://www.dllme.com/dll/download/14091/python27.dll 

   下载python27.dll 放到ida根目录 2.7.8

2. IDAPython: importing "site" failed

   设置环境变量

   ```sh
   #!/bin/sh
   export PYTHONPATH=/usr/lib/python2.7 && wine /home/firmadyne/Documents/IDA68/idaq.exe
   ```

   使用方法：

   root 根目录下运行 sh ida.sh 就可以啦！
   
   （可以再写一个ida64的）

参考：

https://www.cnblogs.com/CoBrAMG/p/9198113.html

https://blog.csdn.net/qq_29343201/article/details/72676372