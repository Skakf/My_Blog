# Vmware -ubuntu终极配网指南

mlgb，饶是我配网小王子，也屡屡折在这配网的奥妙中

最最后还不是要修改 /etc/resolv.conf (白眼)



要自行设定DNS服务器有两个方法

# 关闭systemd-resolvd服务



| 12   | systemctl stop systemd-resolvdsystemctl disable systemd-resolvd |
| ---- | ------------------------------------------------------------ |
|      |                                                              |



这样对/etc/resolv.conf做出的修改都能保存下来。

# 修改systemd-resolv的设置

打开 /etc/systemd/resolved.conf，修改为



| 123456789 | [Resolve]DNS=1.1.1.1 1.0.0.1#FallbackDNS=#Domains=LLMNR=no#MulticastDNS=no#DNSSEC=no#Cache=yes#DNSStubListener=yes |
| --------- | ------------------------------------------------------------ |
|           |                                                              |



DNS=设置的是域名解析服务器的IP地址，这里分别设为1.1.1.1和1.0.0.1
LLMNR=设置的是禁止运行LLMNR(Link-Local Multicast Name Resolution)，否则systemd-resolve会监听5535端口。

