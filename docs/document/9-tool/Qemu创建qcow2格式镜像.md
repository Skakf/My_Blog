# Qemu创建qcow2格式镜像

```shell
创建镜像
qemu-img create -f qcow2 winxp_sp3_x86.qcow2 30G
```

```shell
把iso和镜像绑定，注意vnc默认从5900开始有端口可以连进虚拟机
qemu-system-i386 -m 1024 -cdrom windows_xp_professional_with_service_pack_3_x86_cd_vl_x14-74070.iso -boot menu=on -drive file=winxp_sp3_x86.qcow2  -vnc :1
```

```shell
重新打开终端
vncviewer :5901
```

然后就可以从界面进行安装了。

第二步的时候参数有些网站写了很多，但其实只是个绑定作用，很多可以后面在配置



### references

https://wiki.archlinux.org/title/QEMU_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E8%BF%90%E8%A1%8C%E8%99%9A%E6%8B%9F%E5%8C%96%E7%9A%84%E7%B3%BB%E7%BB%9F