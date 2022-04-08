# 安装 firmafl

make 报错：cannot find ld: lxxx

解决：

1. 缺少xx-dev
2. 软连接不对
3. libxxx

- locate libxxx.so
- ln -s ./x86xxx/libxxx.2.0.so libxxx.so
- 
- libbfd-2.30-system.so 

2。 int memfd_create (const char *__name, unsigned int __flags) __THROW;

https://blog.csdn.net/newnewman80/article/details/90175033

3. 缺少boost/fore.cpp



```sql
sudo apt-get install libboost-all-dev
```

https://github.com/zyw-200/FirmAFL/issues/16