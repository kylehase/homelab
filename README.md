# KyleHase's homelab
A repository of diagrams, descriptions, configs, and scripts for my own reference and to share with anyone interested.

## Overview
My homelab consists of 3 IP networks (Main, Guest, IoT) connected through 2 routers to an NTT fiber terminal for Internet access. A separate Zigbee network is connected to my [Home Assistant](https://www.home-assistant.io/) smarthome hub.

![Network Overview](https://user-images.githubusercontent.com/89047/139237614-52fe8c43-73b1-4b9a-bc4a-efb89a8ddafd.png)

## Internet
The physical line is a 1000Mbps fiber from NTT which is the only option in my building.

Internet is provided by [OCN](https://www.ntt.com/personal/services/internet/hikari/ocnhikari.html) for no particular reason other than it's what I've been using from the start. One benefit I found is that OCN supports simultaneous IPoE and PPPoE connections with a single contract. This proved to be useful as explained later.

## IPoE gateway
The default ISP connection is IPv6-only. IPoE is required to provide **IPv4 over IPv6** in order to access most of the Internet which is on IPv4. This is typically handled by an IPoE-supported router. 

![IPoE Gateway](https://user-images.githubusercontent.com/89047/139237507-5983f957-7662-424c-9ad0-fc410246f249.png)  
<sub>*Concept of IPv4 over IPv6*</sub>

Unfortunately only a [handful of routers support Japanese IPoE implementations](https://24wireless.info/ipv4-over-ipv6-router-list) and none met my requirements for WiFi 6 and advanced networking. Implementing IPoE on a DIY router did not seem [worth the effort](https://qiita.com/kakinaguru_zo/items/2764dd8e83e54a6605f2).

In the end I configured a [Buffalo WSR-1166DHPL2 router](https://www.buffalo.jp/product/detail/wsr-1166dhpl2.html) as an IPoE gateway appliance by disabling the most functions. This enabled me to use any router of my choice behind the IPoE gateway. Below are the settings used on the Buffalo router.

- Enable
  - IPoE (OCN Virtual Connect)
  - IPv6 bridging
  - PPPoe passthrough
- Disable
  - WiFi
  - DHCP
  - IPv4 and IPv6 firewalls
- Set DMZ to an internal IP address which will be the primary router's static WAN address

### Before IPoE (PPPoE)
IPoE is a bit of a hassle but the alternative is PPPoE which has been facing major congestion, especially with the [increase in work from home due to COVID-19](https://www.iij.ad.jp/en/dev/iir/pdf/iir_vol47_focus2_EN.pdf). My measured speeds on an earlier 100Mbps VDSL line were dropping down below 10Mbps at times.

![Screen Shot 2020-09-28 at 5 44 38 PM](https://user-images.githubusercontent.com/89047/139194755-95918dc3-9336-4a36-92dd-3ce8ab9460c0.png)  
<sub>*Effect of congestion on a 100Mbps PPPoE connection*</sub>

## Main LAN & Guest
