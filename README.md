# KyleHase's homelab
A repository of diagrams, descriptions, configs, and scripts for my own reference and to share with anyone interested.

## Overview
My homelab consists of 3 IP networks (Main, Guest, IoT) connected through 2 routers to an NTT fiber terminal for Internet access. A separate Zigbee network is connected to my [Home Assistant](https://www.home-assistant.io/) smarthome hub.

![Network Overview](https://user-images.githubusercontent.com/89047/139237614-52fe8c43-73b1-4b9a-bc4a-efb89a8ddafd.png)

## Internet
The physical line is a 1000Mbps fiber from NTT which is the only option in my building.

Internet is provided by [OCN](https://www.ntt.com/personal/services/internet/hikari/ocnhikari.html) for no particular reason other than it's what I've been using from the start. One benefit I found is that OCN supports simultaneous IPoE and PPPoE connections with a single contract, which proved to be useful as explained later.

## IPoE gateway
This ISP provides DHCP IPv6 addresses by default but IPv4 is required to reach most of the Internet. IPoE (MAP-E) enables **IPv4 over IPv6** and is typically handled by an IPoE-supported router. The IPv4 address is shared and NATted at the ISP but this isn't an issue for day-to-day use.

![IPoE Gateway drawio](https://user-images.githubusercontent.com/89047/139384235-cc1b3aab-9136-4679-8909-28851d3f4b66.png)  
<sub>*Concept of IPv4 over IPv6*</sub>

Only a [handful of routers support Japanese IPoE implementations](https://24wireless.info/ipv4-over-ipv6-router-list) and none of those met my requirements for WiFi 6 and advanced networking. Implementing IPoE on a DIY SBC was possible but not [worth the cost nor effort](https://qiita.com/kakinaguru_zo/items/2764dd8e83e54a6605f2).

The simplest option was to configure an [basic IPoE router](https://www.buffalo.jp/product/detail/wsr-1166dhpl2.html) as an IPoE gateway by disabling most of the router features, leaving WiFi and advanced networking to a separate, more powerful router. The IPoE router settings are as follows:

- Enable
  - IPoE (OCN Virtual Connect)
  - IPv6 bridging
  - PPPoe passthrough
- Disable
  - WiFi
  - DHCP
  - IPv4 and IPv6 firewalls
- Set DMZ to an internal IP address which will be the primary router's static WAN address

The LAN side of the gateway now provides 1 local IPv4 address and passes through all IPv6 addresses.

## Main and Guest LAN
At the core of the main LAN is an [ASUS RT-AX86U](https://www.asus.com/Networking-IoT-Servers/WiFi-Routers/ASUS-Gaming-Routers/RT-AX86U/) running the [Asuswrt-Merlin](https://www.asuswrt-merlin.net/) custom firmware.
![Main Guest LAN drawio](https://user-images.githubusercontent.com/89047/139386235-a5d06939-df85-4b93-aa43-69871eee70d5.png)  
<sub>*Main and Guest LAN*</sub>


### Before IPoE (PPPoE)
Using IPoE is a bit of a hassle but the alternative is PPPoE which has been facing major congestion, especially with the [increase in work from home due to COVID-19](https://www.iij.ad.jp/en/dev/iir/pdf/iir_vol47_focus2_EN.pdf). My measured speeds on an earlier 100Mbps VDSL line were dropping down below 10Mbps at times.

![Screen Shot 2020-09-28 at 5 44 38 PM](https://user-images.githubusercontent.com/89047/139194755-95918dc3-9336-4a36-92dd-3ce8ab9460c0.png)  
<sub>*Effect of congestion on a 100Mbps PPPoE connection*</sub>
