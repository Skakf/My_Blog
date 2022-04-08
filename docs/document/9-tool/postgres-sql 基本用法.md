# postgres-sql 基本用法

安装：sudo apt-get install postgresql

安好之后会默认创建postgres用户，要使用postgresql，需要先切换

切换到postgres用户：sudo -i -u postgres

$ psql

进入postgresql命令行，此时显示：postgres=#    就可以执行各种sql语句了

---

创建用户 ：	sudo -u postgres createuser -P  username  (password 自己设置)

创建数据库： sudo -u postgres createdb -O username dbname

sudo -u postgres psql -d dbname < xx/xx/database/schema

------------------------

登录：					psql -U username -d dbname

列出所有数据库：	\l

查询创建的用户：	\du

列出当前数据库所有表：\d

查看指定表的基本情况（所有字段）：\d+ tablename

查询：			select * from tablename where id = '1';

删除：			delete  from tablename where id ='1';   

退出：			\q

---

又想到了之前借着库里没东西总是智障又暴力的直接删库的（狗头）

删库：		drop database dbname;

---

卸载软件

 sudo apt-get  --purge remove postgresql\\* 

删除配置及相关文件：

 sudo rm -r /etc/postgresql/ 

sudo rm -r /etc/postgresql-common/ 

sudo rm -r /var/lib/postgresql/  

删除用户和所在组：

 sudo userdel -r postgres 

sudo groupdel postgres 

---

FAQ：

报错：psql: FATAL:  Peer authentication failed for user "new_user"

解决：sudo -u new_user psql -U new_user -d new_db

原因： 参考 stackoverflow上有相关答案，原址：[ psql: FATAL: Peer authentication failed for user “dev”](https://link.jianshu.com/?t=https%3A%2F%2Fstackoverflow.com%2Fquestions%2F17443379%2Fpsql-fatal-peer-authentication-failed-for-user-dev) 

psql的连接建立于Unix Socket上默认使用peer authentication，所以必须要用和 **数据库用户** 相同的 **系统用户** 进行登录。

还有一种方法，将peer authentiction 改为 md5，并给数据库设置密码。修改配置文件,
 `/etc/postgresql/x.x/main/pg_hba.conf`

 重新启动pgsql，
`/etc/init.d/postgresql restart` 

 参考：https://www.jianshu.com/p/695bc8ed278d 

