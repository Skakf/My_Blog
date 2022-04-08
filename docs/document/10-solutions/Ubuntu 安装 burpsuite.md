# Ubuntu 安装 burpsuite

1. 安装jdk

```
apt install openjdk-8-jdk-headless
apt install openjdk-8-jre-headless 
```

 然后

```
sudo gedit /etc/java-8-openjdk/accessibility.properties
```

2. 安装java环境

   1）下载 java8  

   http://www.oracle.com/technetwork/cn/java/javase/downloads/jdk8-downloads-2133151-zhs.html

   选择jdk-8u221-linux-x64.tar.gz

   （oracle还要登录，奇怪了）

   2）解压

   `tar zxvf  jdk-8u221-linux-x64.tar.gz 
    mv jdk1.8.0_221 /opt
    cd /bin
    ln -s /opt/jdk1.8.0_221/bin/java java
    cd /etc
    gedit profile`

   在末尾添上：
   `export JAVA_HOME=/opt/jdk1.8.0_221
    export JRE_HOME=${JAVA_HOME}/jre
    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
    export PATH={JAVA_HOME}/bin:$PATH`

   重新执行文件

   `source profile `

   之后输入 `echo $JAVA_HOME`
   出现 `/opt/java1.8.0_221`
   就成功安装了

   

3. 安装burpsuite

   （我有现成的，不然就要去下载burpsuite压缩包）

   把BurpLoader.jar &  burpsuite_pro_vX.X.XX.jar文件新建放在burpsuite里，然后mv到/opt/文件夹下

   `sudo java -jar /opt/burpsuite/BurpLoader.jar`

   就可以直接运行burpsuite了

   

4. 方便使用

   `cd /opt/burpsuite`

   `sudo chmod +x BurpLoader.jar burpsuite_pro_vx.x.xx.jar`

   `sudo vi /usr/bin/burpsuite`

   ```sh
    #!/bin/bash
       java -jar /opt/burpsuite/BurpLoader.jar
   ```

   `sudo chmod +x /usr/bin/burpsuite`

   完成啦！

5. 使用方法：

root目录 ：  burpsuite    

普通目录：sudo burpsuite

（上次装的ida能不能这么用啊，算了，懒得改）



参考链接：

https://www.jianshu.com/p/3e7bb41a1464

https://blog.csdn.net/qq_42357070/article/details/83587589

https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

---

## windows下安装burpsuite

1. 安装java环境 jdk8

2. 配置环境变量

   a) 点击新建，输入以下信息（变量值写自己的安装目录）
   变量名：JAVA_HOME
   变量值：C:\ProgramFiles(x86)\java\jdk1.8

   b) 找到 Path 变量，直接编辑，在最后面添加以下内容
   ;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;

   c) 点击新建，输入以下信息
   变量名：CLASSPATH
   变量值：%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar

3. 重启电脑（不知道是不是必要，反正我是重启了才安好的）

   cmd输入java --version 有显示就说明安好了

4. 此时，burpsuite.jar双击还打不开，需要继续配置一下

   a） 先选择默认jar 用 javaw.exe打开（虽然此时还打不开，但要打开选一下等下regedit才能出现javaw.exe下面的command）

   b）开始菜单输入regedit , 用管理员权限打开，找到 “HKEY_CLASSES_ROOT\Applications\javaw.exe\shell\open\command”，在其中文件打开命令中加入参数 -jar，修改后的数值类似：““C:\Program Files\Java\jdk8\bin\javaw.exe” -jar “%1””（只需要添加-jar参数，无需修改其他信息），保存并退出注册表编辑器。

5. 这个时候应该就可以顺利打开burpsuite了

6. 配置抓包环境

   a)先burp导出证书（proxy-options / http://burp）

   *PS：好像破解版的burp没办法导出证书？所以我又下载了一个新的……* 

   b)火狐或者ie 选项-证书 导入证书

   这样就能抓https的包啦

   

   Ubuntu下也是同样的操作



参考链接：

https://blog.csdn.net/Toufahaizai/article/details/88637542

https://blog.csdn.net/Toufahaizai/article/details/88637542

https://www.cnblogs.com/v1vvwv/p/how-to-install-BurpSuite.html

https://blog.csdn.net/supassxu/article/details/81448908