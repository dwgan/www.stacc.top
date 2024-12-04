---
layout: default
title: Home
---

<p align="center" style="display: flex; justify-content: space-between; align-items: center; width: 100%;">
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241204142434558.png" style="zoom: 10%;" />
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241204144324126.png" style="zoom:15%;" />
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/io-link-tech.png" style="zoom:12%;" />
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/Subbrand%20STM32.svg" style="zoom: 15%;" />
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/Subbrand_White_STWLC.svg" style="zoom: 18%;" />
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/Subbrand_White_ST25.svg" style="zoom: 18%;" />
  <img src="https://cdn.jsdelivr.net/gh/dwgan/PicGo/img/image-20241204142502018.png" style="zoom: 11%;" />
  <div style="clear: both;"></div>
</p>

Hi there, welcome to the technical zone of **Automation Competence Center**, belonging to **STMicroelectronics**. Our work mainly focus on **smart industrial** and **smart home, building automation**. 

Our mission is design more system solution for customers using our ST's products including but not limits of MCU, MPU, wireless connectivity, converter, sensor, and etc.

Here are some topics that I recently focus on

## STM32MPU

In this project, we explored the advantage of STM32MPU to design powerful development platform with lower price. The STM32MP157 is a heterogeneous system architecture with an A7 core and an M4 core. The A7 core can run a Linux system and execute high-performance tasks such as human-machine interfaces (HMI), video encoding/decoding, and providing USB and Ethernet interfaces. The M4 core can run real-time tasks such as motor control and sensor data acquisition. [Learn more]({{ site.baseurl }}{% post_url 2024-12-03-STM32MP-Development %})

<p align="center">
  <img src="https://dwgan.top/PicGo/img/202409210037469.png" style="zoom: 33%;" />
</p>



## STKNX RFID Reader

One of serials that we recently focus on is STKNX. KNX is a international standard protocol in smart home automation field, which provides a full standard from physical layer to application layer. The full standard allow easy use without considering the compatibility of the products, realizing plug and play.

RFID is one of the most widely used radio frequency communication technologies today, playing an important role in areas such as payment and security. We aim to integrate it into smart industrial and smart home applications. Our team has provided a demo that showcases RFID communication and serves as a reference solution for smart industrial and smart home systems, primarily based on ST's microchips. [Learn more](https://github.com/dwgan/STKNX-RFID-Reader)

<p align="center">
  <img src="https://dwgan.top/PicGo/img/image-20240628185032085.png" style="zoom: 18%;" />
</p>


## STKNX CO2 Sensor

This project provides a reference solution of connecting sensors into KNX system via UART. [learn more](https://github.com/dwgan/STKNX_CO2Sensor)

<p align="center">
	<img src="https://dwgan.top/PicGo/img/image-20240628185247365.png" alt="image-20240117221213953" style="zoom:15%;" />
</p>



## STKNX Charging Station

Nowadays, EV car are more and more popular, but the number of EV charge station is under development. One of the most serious reason is that different suppliers of EV chargers are not able to put together in a system since different suppliers own their own private protocol. 

We introduce KNX into the system, which allow different EV charger from different suppliers exchange data if they use KNX protocol. This platforms  will help to have more EV chargers more long term in terms of sustainability because in a system integrators will not care about the the supplier of EV charger, but they are just they just need to be concerned that the EV charger is compliant to can existing standard. And today existing standard already have all this available to be deployed for energy management system and in particular EV chargers that will help again the deployment of more EV. [Learn more](https://github.com/dwgan/STKNX_ChargeStation)

<p align="center">
  <img src="https://dwgan.top/PicGo/img/image-20240711175747573.png" style="zoom: 60%;" />
</p>


## NAT Traversal

In order to realize smart home / building automation, including remote control and data storage, we employ a Synology based data center. To realize remote data monitor, we need to explore data interface to Internet while keep safety of our data. We build our own NAT traversal server to provide the interface. [Learn more]({{ site.baseurl }}{% post_url 2024-06-26-Synology-NAT %})

<p align="center">
  <img src="https://dwgan.top/PicGo/img/202409210040470.png" style="zoom: 50%;" />
</p>


## Ultra Low Power Energy Harvesting

Green energy usage is a key topic in industrial development. Solar energy, as a form of green energy, is versatile and convenient for use, ranging from personal electronics to industrial devices, from basic equipment such as streetlights to outer space technologies.

In certain specific environments, such as areas lit by incandescent bulbs indoors, the light is too weak to be effectively harvested for energy. A practical and cost-effective solution would be highly beneficial, such as developing advanced light-harvesting technologies that can efficiently convert low-intensity indoor lighting into usable energy. This approach could enable sustainable energy usage in indoor environments and other areas with limited light sources.

The SPV1050 is an ultra-low power and high-efficiency power manager embedding four MOSFETs for boost or buck-boost DC-DC converter and an additional transistor for the load connection/disconnection. An internal high accuracy MPPT algorithm can be used to maximize the power extracted from PV panel or TEG. The internal logic works to guarantee tight monitoring of both the end-of-charge voltage (VEOC) and the minimum battery voltage (VUVP) by opening the pass-transistor at triggering of the VEOC threshold or at triggering of the VUVP threshold to preserve the battery life.

<p align="center">
  <img src="https://dwgan.top/PicGo/img/image-20240924145737780.png" style="zoom: 50%;" />
</p>


