# KyleHase's homelab
A repository of diagrams, descriptions, configs, and scripts for my own reference and to share with anyone interested.

## Overview
My homelab consists of 3 IP networks (Main, Guest, IoT) connected through 2 routers to an NTT fiber terminal for Internet access. A Zigbee network is connected to my [Home Assistant](https://www.home-assistant.io/) smarthome hub.

![network overview](https://user-images.githubusercontent.com/89047/139188396-1a2aff78-ff4d-4ef8-b86a-352fb6a7af0a.png)

## Physical line and ISP
The physical line was upgraded in August 2021 from 100Mbps VDSL to 1000Mbps fiber via NTT.

The ISP is [OCN](https://www.ntt.com/personal/services/internet/hikari/ocnhikari.html) for no particular reason other than it's what I've been using from the start. One benefit I found is that OCN supports simultaneous IPoE and PPPoE connections with a single contract. This proved to be very useful as explained later.

## IPoE appliance
The IPoE appliance enables the use of **IPv4 over IPv6**, in my case branded as "OCN Virtual Connect". Typically, customers would use a router with IPoE built-in but none of those routers met my requirements. I considered building a router but [implementing IPoE did not seem worth the effort](https://qiita.com/kakinaguru_zo/items/2764dd8e83e54a6605f2).

![IPoE](https://user-images.githubusercontent.com/89047/139207691-2a3cb10f-cc99-4566-8e62-e3e4d433c26d.png)  
<sub>*Concept of IPv4 over IPv6*</sub>

In the end I used an [IPoE-supported router](https://www.buffalo.jp/product/detail/wsr-1166dhpl2.html) with Gigabit WAN as a gateway appliance by setting the following:
- Enable IPoE (OCN Virtual Connect)
- Disable WiFi
- Disable DHCP
- Allow IPv6 bridging
- Disable DHCP
- Disable IPv4 and IPv6 firewalls
- Enable PPPoE passthrough
- Set DMZ to an internal IP address which will be the primary rotuer's static WAN address

### Before IPoE (PPPoE)
PPPoE connections have been facing major congestion, especially with the [increase in work from home due to COVID-19](https://www.iij.ad.jp/en/dev/iir/pdf/iir_vol47_focus2_EN.pdf). My measured speeds on a 100Mbps vDSL connection were dropping down below 10Mbps.

![Screen Shot 2020-09-28 at 5 44 38 PM](https://user-images.githubusercontent.com/89047/139194755-95918dc3-9336-4a36-92dd-3ce8ab9460c0.png)  
<sub>*Effect of congestion on a 100Mbps PPPoE connection*</sub>

## Main LAN & Guest
