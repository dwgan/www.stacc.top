---
layout: post
title: "如何在群晖上部署内网穿透，实现Home Assistant的远程访问"
date: 2024-06-26
---

**HomeAssistant**是一个开源的的智能家居平台，通过它可以很方便实现对智能家居的远程监测和控制。

**群晖**NAS提供了一个很丰富的生态，可以很方便快捷的部署数据存储服务。

将**HomeAssistant**部署到群晖上可以很方便快捷的进行数据管理。

然而，系统本身只能在局域网下访问，给**远程控制**带来了一些不便。

内网穿透（NAT 穿透）技术可以将局域网下的特定端口映射到公网，从而实现互联网无障碍访问，实现真正的**智能家居**。

本文主要介绍如何在群晖上部署内网穿透服务。

## 一、方案选择

内网穿透有几种主要方案，按照技术分为**端口映射**、**动态 DNS**、**反向代理**、**VPN**、**Ngrok**、**frp**等，无论哪种技术，都需要一个公网IP作为中转。

按照具体部署方式，主要分为：

1、向内网穿透服务提供商购买内网穿透服务，通过厂商的带有公网IP的VPS进行代理，一般按照流量、带宽、节点数进行付费。该方案实现起来相对方便，由于都是公用带宽，所以用户体验一般，同时安全性相对可靠。

2、向VPS服务商购买带有公网IP的VPS，然后在VPS上搭建代理服务，一般按照VPS配置按年付费。该方式安全性高，用户体验好，对技术要求较高。

3、使用免费的内网穿透，一些厂商会提供免费的服务用于吸引用户，例如：Sakura、Cpolar。该方案免费，但不适合要求较高的应用。

这里主要是用于控制家居的开关，因此数据量非常小，同时考虑到成本，这里尝试了后两种方案。

## 二、自建frp服务器

### 2.2 frpc配置

连接上群晖，在桌面新建一个文件`frpc.ini`，内容如下

```shell
[common]
server_addr = [公网IP]
server_port = [默认是7000]
tcp_mux = true
protocol = tcp
user = [用户名]
token = [验证密钥]
dns_server = 114.114.114.114

[DS923] # 隧道的标识，不能重复
type = tcp
local_ip = 192.168.0.199
local_port = 5000
remote_port = 25000
use_encryption = true
use_compression = true

[Home Assistant]
type = tcp
local_ip = 192.168.0.10
local_port = 8123
remote_port = 28123
use_encryption = true
use_compression = true
```

在群晖`file station`中新建如下文件夹，并且将`frpc.ini`拖到文件夹中

![image-20240729182600741](https://dwgan.top/PicGo/img/image-20240729182600741.png)

在套件中心搜索安装docker，新版的改名为Container Manager了

![image-20240729182700780](https://dwgan.top/PicGo/img/image-20240729182700780.png)

打开docker，搜索安装`frp`，我这里选择`0.50.0`这个版本，注意要和frps的版本匹配

![image-20240729182641515](https://dwgan.top/PicGo/img/image-20240729182641515.png)

安装后启动它，在存储空间设置添加如下内容，将`file station`中的文件映射到docker容器中

![image-20240729183118098](https://dwgan.top/PicGo/img/image-20240729183118098.png)

启动docker，可以在frps管理界面看到已经连接上了

![image-20240729183305905](https://dwgan.top/PicGo/img/image-20240729183305905.png)

尝试网页打开，已经可以连接上了

![image-20240729183446859](https://dwgan.top/PicGo/img/image-20240729183446859.png)

> [内网穿透Nas 基于Frp实现群晖的远程访问 – Frps.cn 中文文档](https://frps.cn/41.html)



## 三、基于Cpolar

### 3.1 下载安装包

1、cpolar群晖套件下载地址：https://www.cpolar.com/synology-cpolar-suite

2、没有DS923的型号，这里选择DS920+替代。

![image-20240627154924012](https://dwgan.top/PicGo/img/image-20240627154924012.png)

3、选择手动安装并且上传

![image-20240627155013558](https://dwgan.top/PicGo/img/image-20240627155013558.png)

![image-20240627155033781](https://dwgan.top/PicGo/img/image-20240627155033781.png)

4、点击同意并且完成

![image-20240627155041301](https://dwgan.top/PicGo/img/image-20240627155041301.png)

![image-20240627155131327](https://dwgan.top/PicGo/img/image-20240627155131327.png)



5、在已安装中找到cpolar

![image-20240627155151390](https://dwgan.top/PicGo/img/image-20240627155151390.png)



6、打开cpolar的网址进入dashboard

![image-20240627155206262](https://dwgan.top/PicGo/img/image-20240627155206262.png)

7、登录已注册的cpolar账号（如果没有请先注册），注意：第一次登录的账号将被记录到系统中，再次更改需要重新安装

![image-20240627155215194](https://dwgan.top/PicGo/img/image-20240627155215194.png)

### 3.2 配置网络

1、配置Home Assistant的IP和端口，IP可以在群晖中找到，端口默认是8123

![image-20240627155230215](https://dwgan.top/PicGo/img/image-20240627155230215.png)

![image-20240627155248867](https://dwgan.top/PicGo/img/image-20240627155248867.png)

2、配置群晖的端口为5001

![image-20240627155300755](https://dwgan.top/PicGo/img/image-20240627155300755.png)

3、可以在状态->在线隧道列表中找到配置好的连接

![image-20240627155307914](https://dwgan.top/PicGo/img/image-20240627155307914.png)

![image-20240716143422496](https://dwgan.top/PicGo/img/image-20240716143422496.png)

4、修改Home Assistant中的代理设置

![image-20240716114750268](https://dwgan.top/PicGo/img/image-20240716114750268.png)

5、使用浏览器连接，可以打开群晖管理界面以及home assistant界面

![image-20240627155314709](https://dwgan.top/PicGo/img/image-20240627155314709.png)

![image-20240627155331985](https://dwgan.top/PicGo/img/image-20240627155331985.png)

完结！

## 参考

> [如何在群晖系统中安装cpolar（群晖7.X版） - cpolar 极点云官网](https://www.cpolar.com/blog/how-to-install-cpolar-on-a-synology-system-cfah-version-7-x)

> [如何搭建Home Assistant智能家居系统并通过内网穿透实现远程控制家中设备 - cpolar 极点云官网](https://www.cpolar.com/blog/how-to-build-a-home-assistant-smart-home-system-and-remotely-control-home-devices-through-intranet-penetration)