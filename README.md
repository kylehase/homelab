# KyleHase's homelab
A repository of diagrams, descriptions, configs, and scripts for my own reference and to share with anyone interested.

## Overview
My homelab consists of 3 IP networks (Main, Guest, IoT) connected through 2 routers to an NTT fiber terminal for Internet access. A separate Zigbee network is connected to my [Home Assistant](https://www.home-assistant.io/) smarthome hub.

![Network Overview drawio](https://user-images.githubusercontent.com/89047/139588260-61430b02-ac6b-46bc-9baf-fff48e0ae204.png)

## Internet
The physical line is a 1000Mbps fiber from NTT which is the only option in my building.

Internet is provided by [OCN](https://www.ntt.com/personal/services/internet/hikari/ocnhikari.html) for no particular reason other than it's what I've been using from the start. One benefit I found is that OCN supports simultaneous IPoE and PPPoE connections with a single contract, which proved to be useful as explained later.

### Connection methods
This ISP provides DHCP IPv6 addresses by default but IPv4 is required to reach most of the Internet. IPoE (MAP-E) enables **IPv4 over IPv6** and is typically handled by an IPoE-supported router. The IPv4 address is shared and NATted at the ISP but this isn't an issue for day-to-day use.

![Connection methods drawio](https://user-images.githubusercontent.com/89047/139609881-1550673d-1986-4ab5-ae79-6021488e837c.png)  
<sub>*Connection methods*</sub>


Legacy PPPoE connections are also supported but suffer major congestion especially during peak hours, so IPoE is by far the preferred connection method for speed and latency.

![Screen Shot 2020-09-28 at 5 44 38 PM](https://user-images.githubusercontent.com/89047/139194755-95918dc3-9336-4a36-92dd-3ce8ab9460c0.png)  
<sub>*Effect of congestion on a 100Mbps PPPoE connection*</sub>

### IPoE Gateway
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

## LANs
3 LANs provide security and enable application of systematic policies.

The primary LAN hosts trusted devices like NAS, laptops, and mobile. A few trusted IoT devices like Nvidia Shield and Nest Home are also on this LAN as they require access to the NAS and multicast from trusted devices.

![LANs](https://user-images.githubusercontent.com/89047/139636248-c48a94fb-c9f2-4401-bef9-8bfdcd192a90.png)  
<sub>*Topology*</sub>

At the core of the main LAN is an [ASUS RT-AX86U](https://www.asus.com/Networking-IoT-Servers/WiFi-Routers/ASUS-Gaming-Routers/RT-AX86U/) running the [Asuswrt-Merlin](https://www.asuswrt-merlin.net/) custom firmware.
