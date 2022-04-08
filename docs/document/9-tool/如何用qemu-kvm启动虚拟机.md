# 如何用qemu-kvm启动虚拟机

1. qemu-img create vm1.img 6G 创建镜像

2. 编写xml文件(比较通用) 或者拥有镜像.img

3. 修改xml文件

4. 模板

   - name -虚拟机名称
   -  memory-内存大小
   - <emulator>/usr/bin/qemu-system-x86_64</emulator> 路径要locate qemu-system-x86_64看一下你的在哪里
   - file='/home/skakf/sss/images/ubuntu1604_resize.img' 改成镜像路径
   - <interface type='bridge'>   
           <source bridge='br0'/>     网桥
   - VNC port 端口

   ~~~xml
   <domain type='kvm'>
     <name>test</name>
     <uuid>e5fff551-bbe1-e748-c8e4-8ecb3bff1604</uuid>
     <memory unit='KiB'>2097152</memory>
     <currentMemory unit='KiB'>2097152</currentMemory>
     <vcpu>2</vcpu>
     <os>
       <type arch='x86_64' machine='pc'>hvm</type>
       <boot dev='hd'/>
   	<boot dev='cdrom'/>
     </os>
     <features>
   	  <acpi/>
   	  <apic/>
   	  <pae/>
     </features>
     <clock offset="localtime"/>
     <on_poweroff>destroy</on_poweroff>
     <on_reboot>restart</on_reboot>
     <on_crash>destroy</on_crash>
     <devices>
       <emulator>/usr/bin/qemu-system-x86_64</emulator>
       <disk type='file' device='disk'>
         <driver name='qemu' type='qcow2'/> 
         <source file='/home/skakf/sss/images/ubuntu1604_resize.img'/>
         <target dev='hda' bus='ide'/>
      </disk>
       <interface type='bridge'>   
         <source bridge='br0'/>     
       </interface>
       <input type='mouse' bus='ps2'/>
       <graphics type='vnc' port='5906' autoport='no' listen='0.0.0.0'/>
       <console type='pty'/>
     </devices>
   </domain>
   ~~~

   5. 改好之后， virsh create test.xml
   6.  vncviewer :6
   7. virsh list   virsh destroy virsh shutdown

---

也可以用virt-manager创建虚拟机 方便快捷

kvm也可，但我不会 （狗头🐶）

