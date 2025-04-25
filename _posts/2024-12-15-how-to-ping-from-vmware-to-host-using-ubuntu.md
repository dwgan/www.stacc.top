---
title: "使用虚拟机开发的局域网设置"
last_modified_at: 2024-12-20T16:00:58-04:00
permalink: /posts/how-to-ping-from-vmware-to-host-using-ubuntu/
redirect_from:
  - /theme-setup/
toc: true
---

由于开发通常在Linux环境下进行，而我们常用的主机是Windows，因此需要利用Windows虚拟机来配置开发环境，这样可以很好的隔离开发环境。但是，由于虚拟机中默认的网络环境和STM32MP嵌入式开发板不在一个局域网下，因此无法直接通信，通过配置，可以实现STM32MP嵌入式开发板、Windows主机以及虚拟机三者在同一个局域网下，同时它们都能够连接互联网。

如图是我的物理连接：

本教程以Vmware虚拟机软件为例，Linux系统采用Ubuntu22.04.5，以下是具体操作步骤：

## 一、共享Wifi连接到以太网口
### Windows+Ubuntu虚拟机参考
首先共享WiFi连接到以太网口，在网络共享中心中选择可以上互联网的网络，点击属性->共享->勾选允许其他网络->选择以太网

<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image.png" style="zoom: 100%;" />
</p>


可以看到以太网的ip变成了192.168.137.1

<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241223115506355.png" style="zoom: 100%;" />
</p>

### Ubuntu物理机参考

通过以下步骤共享无线网络（wlp1s0）到有线网卡（eno1），并设置 eno1 的 IP 为 `192.168.20.1`：

步骤 1：设置 eno1 的静态 IP
```bash
sudo ifconfig eno1 192.168.20.1 netmask 255.255.255.0 up
```
这会为 `eno1` 设置 IP `192.168.20.1`，子网掩码 `255.255.255.0`。

步骤 2：启用 IP 转发
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
这会临时启用 IPv4 转发（重启后失效）。

步骤 3：配置 iptables NAT 规则
```bash
sudo iptables -t nat -A POSTROUTING -o wlp1s0 -j MASQUERADE
sudo iptables -A FORWARD -i eno1 -o wlp1s0 -j ACCEPT
sudo iptables -A FORWARD -i wlp1s0 -o eno1 -m state --state RELATED,ESTABLISHED -j ACCEPT
```
这些规则会：
1. 将来自 `eno1` 的流量通过 `wlp1s0` 进行 NAT。
2. 允许双向流量转发。

步骤 4：验证配置
1. 检查 eno1 的 IP：
   ```bash
   ifconfig eno1
   ```
   应显示 `inet 192.168.20.1`。

2. 检查 IP 转发是否启用：
   ```bash
   cat /proc/sys/net/ipv4/ip_forward
   ```
   输出应为 `1`。

3. 检查 iptables 规则：
   ```bash
   sudo iptables -t nat -L -v
   sudo iptables -L -v
   ```

步骤 5（可选）：持久化配置
如果希望配置永久生效：
1. 永久启用 IP 转发：
   ```bash
   echo "net.ipv4.ip_forward=1" | sudo tee /etc/sysctl.d/99-ip-forward.conf
   sudo sysctl -p
   ```

2. 保存 iptables 规则（依赖系统工具如 `iptables-persistent`）：
   ```bash
   sudo apt install iptables-persistent -y
   sudo netfilter-persistent save
   ```


## 二、将虚拟网络连接至以太网

在VMware中打开虚拟网络编辑。通常，VMware通过VMnet0与宿主机连接。在VMware中选择Edit->Virtual Network Editor->Change Settings->VMnet0->Bridged to

<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241223115617138.png" style="zoom: 100%;" />
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241203162302053.png" style="zoom: 100%;" />
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241223115824726.png" style="zoom: 100%;" />
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241203162410872.png" style="zoom: 100%;" />
</p>


## 三、编辑虚拟机网络连接

2、桥接到你实际要用的网卡上，打开任务管理器，可以看到对应的网卡名称。点击OK，打开虚拟机

<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241223115824726.png" style="zoom: 100%;" />
</p>
编辑虚拟机网络连接，选择用户自定义->VMnet0

<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241223120249069.png" style="zoom: 100%;" />
</p>

## 四、在Ubuntu中连接网络

<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241223133625629.png" style="zoom: 100%;" />
</p>

在命令行中查看当前ip，如果出现分配的IPV4地址说明设置成功

```shell
acc@acc-virtual-machine:~$ ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.137.2  netmask 255.255.255.0  broadcast 192.168.137.255
        inet6 fe80::9ce4:25c6:b4bc:b401  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:a1:53:07  txqueuelen 1000  (Ethernet)
        RX packets 1501  bytes 267972 (267.9 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 694  bytes 91426 (91.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 953  bytes 73571 (73.5 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 953  bytes 73571 (73.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

acc@acc-virtual-machine:~$ 
```

## 五、在STM32MP开发板中连接网络

在STM32MP开发板中设置ip地址，我这里使用的是NetworkManger

```shell
nmcli connection modify eth0 ipv4.addresses 192.168.137.3/24
nmcli connection modify eth0 ipv4.gateway 192.168.137.1
nmcli connection modify eth0 ipv4.dns "8.8.8.8 8.8.4.4"
nmcli connection modify eth0 ipv4.method manual
```

查看一下网络状态

```shell
root@ccmp15-dvk:~# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:04:F3:4E:14:24
          inet addr:192.168.137.3  Bcast:192.168.137.255  Mask:255.255.255.0
          inet6 addr: fe80::204:f3ff:fe4e:1424/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1060 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1108 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:189791 (185.3 KiB)  TX bytes:159335 (155.6 KiB)
          Interrupt:57 Base address:0xc000

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:1397 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1397 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:103836 (101.4 KiB)  TX bytes:103836 (101.4 KiB)

wlan0     Link encap:Ethernet  HWaddr 00:04:F3:4E:14:25
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

---

如果`NetworkManger`不可用，可以使用`systemd-networkd`，打开`10-end0.network`文件，注意`end0`是网络的接口名字，可通过`ifconfig`获取

```sh
root@stm32mp2-e3-e0-f5:~# ifconfig
end0      Link encap:Ethernet  HWaddr 10:E7:7A:E3:E0:F5
          inet addr:192.168.20.200  Bcast:192.168.20.255  Mask:255.255.255.0
          inet6 addr: fe80::12e7:7aff:fee3:e0f5/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:39 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:6912 (6.7 KiB)
          Interrupt:61 Base address:0x8000
```

编辑配置文件

```shell
root@stm32mp2-e3-e0-f5:/etc/network# vi /etc/systemd/network/10-end0.network
```

填入以下内容

```
[Match]
Name=end0

[Network]
Address=192.168.20.200/24
Gateway=192.168.20.1
DNS=223.5.5.5
DNS=8.8.8.8
```

重启网络管理器使之生效

```
systemctl daemon-reload
systemctl restart systemd-networkd
```


## 六、网络连接测试

尝试从STM32MP开发板中ping一下Windows主机、Ubuntu虚拟机以及百度

```shell

root@ccmp15-dvk:~# ping 192.168.137.1
PING 192.168.137.1 (192.168.137.1): 56 data bytes
64 bytes from 192.168.137.1: seq=0 ttl=128 time=0.762 ms
64 bytes from 192.168.137.1: seq=1 ttl=128 time=0.708 ms
64 bytes from 192.168.137.1: seq=2 ttl=128 time=0.840 ms
^C
--- 192.168.137.1 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.708/0.770/0.840 ms
root@ccmp15-dvk:~# ping 192.168.137.2
PING 192.168.137.2 (192.168.137.2): 56 data bytes
64 bytes from 192.168.137.2: seq=0 ttl=63 time=1.218 ms
64 bytes from 192.168.137.2: seq=0 ttl=64 time=1.305 ms (DUP!)
64 bytes from 192.168.137.2: seq=0 ttl=63 time=1.349 ms (DUP!)
64 bytes from 192.168.137.2: seq=0 ttl=64 time=1.390 ms (DUP!)
64 bytes from 192.168.137.2: seq=1 ttl=63 time=1.615 ms
64 bytes from 192.168.137.2: seq=1 ttl=64 time=1.717 ms (DUP!)
64 bytes from 192.168.137.2: seq=1 ttl=63 time=2.064 ms (DUP!)
64 bytes from 192.168.137.2: seq=1 ttl=64 time=2.119 ms (DUP!)
^C
--- 192.168.137.2 ping statistics ---
2 packets transmitted, 2 packets received, 6 duplicates, 0% packet loss
round-trip min/avg/max = 1.218/1.597/2.119 ms
root@ccmp15-dvk:~# ping baidu.com
PING baidu.com (110.242.68.66): 56 data bytes
64 bytes from 110.242.68.66: seq=0 ttl=49 time=48.773 ms
64 bytes from 110.242.68.66: seq=1 ttl=49 time=48.933 ms
64 bytes from 110.242.68.66: seq=2 ttl=49 time=49.842 ms
^C
--- baidu.com ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 48.773/49.182/49.842 ms
root@ccmp15-dvk:~#

```

从Ubuntu虚拟机中ping一下Windows主机、STM32MP开发板以及百度

```shell
acc@acc-virtual-machine:~$ ping 192.168.137.1
PING 192.168.137.1 (192.168.137.1) 56(84) bytes of data.
64 bytes from 192.168.137.1: icmp_seq=1 ttl=128 time=0.993 ms
64 bytes from 192.168.137.1: icmp_seq=2 ttl=128 time=0.796 ms
^C
--- 192.168.137.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.796/0.894/0.993/0.098 ms
acc@acc-virtual-machine:~$ ping 192.168.137.3
PING 192.168.137.3 (192.168.137.3) 56(84) bytes of data.
From 192.168.137.1 icmp_seq=1 Redirect Network(New nexthop: 192.168.137.216)
64 bytes from 192.168.137.3: icmp_seq=1 ttl=64 time=0.914 ms
64 bytes from 192.168.137.3: icmp_seq=1 ttl=63 time=0.914 ms (DUP!)
64 bytes from 192.168.137.3: icmp_seq=1 ttl=64 time=1.08 ms (DUP!)
64 bytes from 192.168.137.3: icmp_seq=1 ttl=63 time=1.08 ms (DUP!)
From 192.168.137.1 icmp_seq=2 Redirect Network(New nexthop: 192.168.137.3)
64 bytes from 192.168.137.3: icmp_seq=2 ttl=64 time=1.28 ms
64 bytes from 192.168.137.3: icmp_seq=2 ttl=63 time=1.28 ms (DUP!)
64 bytes from 192.168.137.3: icmp_seq=2 ttl=64 time=1.28 ms (DUP!)
64 bytes from 192.168.137.3: icmp_seq=2 ttl=63 time=1.28 ms (DUP!)
^C
--- 192.168.137.216 ping statistics ---
2 packets transmitted, 2 received, +6 duplicates, +2 errors, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.914/1.137/1.275/0.150 ms
acc@acc-virtual-machine:~$ ping baidu.com
PING baidu.com (110.242.68.66) 56(84) bytes of data.
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=1 ttl=49 time=48.3 ms
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=2 ttl=49 time=47.6 ms
64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=3 ttl=49 time=48.4 ms
^C
--- baidu.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2005ms
rtt min/avg/max/mdev = 47.602/48.097/48.382/0.351 ms
acc@acc-virtual-machine:~$ 

```

## 注意

若出现无法ping通主机的情况，尝试关闭Windows的公共网络下的防火墙试试


<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241223125921720.png" style="zoom: 100%;" />
</p>
