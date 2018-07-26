*  开启机器虚拟化支持

* 排查虚拟机不能联网问题

1. 设置VM软件本身： 编辑 -> 虚拟网络编辑器, VMnet0 桥接模式, 桥接到指定网卡
   > 还原默认设置

2. 设置指定虚拟机： 网络适配器, 选择桥接模式， 勾选复制物理网络连接状态
   > 删除网络适配器，重新添加

3. 关闭宿主主机防火墙

4. 关闭linux防火墙, 关闭SELINUX

5. route -n 查看是否有网关路由

6. IP 是否和网络环境冲突、 更换一个试试

7. 宿主主机和linux可以不在一个网段, 只要能上网就 OK

8. 检查IP配置 cat /etc/sysconfig/network-scripts/ifcfg-ens33

```
TYPE=Ethernet
BOOTPROTO=static
#BOOTPROTO=dhcp
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=yes

NAME=ens33
UUID=4b9cf726-1013-4c51-8490-f7bfcc8749dc
DEVICE=ens33
HWADDR=00:0c:29:12:ef:65

IPADDR=10.88.1.15
NETMASK=255.255.252.0
NETWORK=10.88.1.0
GATEWAY=10.88.0.1
ONBOOT=yes

DNS1=10.88.10.10
DNS2=10.88.10.15
DNS3=223.5.5.5 
```
> service network restart
> 重启网卡

9. 检查DNS解析配置 cat /etc/resolv.conf 

```
nameserver 10.88.10.10
nameserver 10.88.10.15
nameserver 223.5.5.5
```

10. 检查网络配置 cat /etc/sysconfig/network

```
NETWORKING=yes
HOSTNAME=localhost.localdomain
NETWORKING_IPV6=no
GATEWAY=10.88.0.1
```
> 注意,  HOSTNAME要和HOST一致

11. 




