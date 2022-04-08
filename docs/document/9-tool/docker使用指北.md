# docker使用指南-部署web环境

## 前言

​	前人栽树，后人乘凉，小菜鸡靠着自身聪明的头脑，坚持不懈地努力，以及遇到瓶颈时的出卖可爱，也算是实现了半只脚docker入门。特写下docker使用指北，以供后续参考

## 简介

docker-容器

## 命令

compose

lnp-nginx,php,集合的镜像

本次环境部署涉及的一些命令

docker ps

docker-compose up

docker-compose up -d

docker-compose down

docker-compose stop

docker save -o XG001.tar XG001

docker commit

过程就是，

1. 针对一道web题，先使其能本地能运行，页面都写好

2. https://gitee.com/koma/docker-lnp/repository/archive/master.zip，gitee有个docker-lnp，集合了nginx,php，

按照教程把它搞下来，就有了基本环境

3. docker ps - 查看当前运行的docker
4. docker commit xxxxxxxx xgs0f02   - 保存镜像
5. 建立一个文件夹（例如：/root/workspace/ctf-web01）
6. 修改dockerfile里的 端口信息，12025 / echo.php里面的端口信息 12025 ，还可以添加本地端口映射到docker端口，本地文件夹映射到docker文件夹： /www: /var/www
7. 出web题目是记得把flag再往下藏一个目录，以免别人绕过直接在url里/flag.html就找到了
8. docker save -o xxx.tar xxx  -保存docker镜像
9. docker-compose up -d  -在docker当前文件夹下启动机器
10. 访问网址localhost:12025,可打开网页
11. docker-compose down 关闭注销机器 / docker-compose stop

