---
title: Networking
parent: Networking
grand_parent: Misc
nav_order: 100
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# Networking 

This page is dedicated to all my notes / research dedicated to learning the foundational concepts of computer networking. My intention with this page is to serve as a reference while working on my hypervisor and implementing new tools / technologies to make my setup more complex in what it can do.

# Big Endian vs Little endian 

Networks send bytes in Big Endian or *Network Byte Order* , meaning the bytes are read as they are stored in memory. Therefore in the example below is a memory address. 

`0x12345678`

This address is stored in memory as : 

12 34 56 78 

The most signifigant bit ( MSB ) is sent first ( 12 ). If you were to read the bits as a computer reading them in host byte order, you would read them left to right.

Little Endian or *Host Byte Order* is the opposite. Reading bytes from right to left.  

# Autonegotiation 

Autonegotiation is a feature that allows a port on a router / server / switch / server to negotiate with the device on the other end of a link to determine the best duplex setting and speed. A driver then configures the interface to the values negotiated for the link. There are two parameters I mentioned before for negotiation. Those two being the speed and the duplex setting. 

Speeds are negotiated in Megabits per second ( Mbps ). in multiples of 10 ( 10 , 100 , 1000 , 10000).

Duplex determines how data flows on the device , half duplex means that only one side can trasmit data while the other cannot. Full duplex means that both sides can transmit & receive data at the same time.

There is a common misconception about autonegotiation, that being the misconception that autonegotiation configures the other host on the opposite side of the link. Autonegotiation only works if BOTH sides of the link are implementing autonegotiation. If both sides aren't using autonegotiate , then autonegotiate cannot determine the speed / duplex of the switch. 
If both sides are using autonegotiate , then one device advertises the speed / duplex to the other device on the link. The configuration is done on the hosts that have the feature enabled.

There is a common issue with autonegotiation being "parallel detection" which is enabled once autonegotiation fails to find autonegotiation running on the other side of the link.

Parallel detection works by sending a signal to the 10Base-T , 100Base-TX , 100Base-T4 drivers on the switch. If any of the drivers detect the signal , the interface is set to the speed mentioned on the signal. Keep in mind, parallel detection only changes the speed on the interface and **NOT** the duplex. You should keep this in mind as the way duplex is set differs based on the ethernet standard used.


## 10Base-T

Originally designed without full-duplex support. Some do , but most do not.

## 100Base-T

Has always had fully supported full-duplex however, by default however the standard will use half-duplex until set manually.  

## 1000Base-T 

This standard has more support for autonegotiation. In general links using this standard should be left to autonegotiate.

## 10 Gigabit 

Links using this standard are more dependent on fiber transcievers or specialized copper connectors which dictates how 10G connects. 

on a 6500 switch they usually require XENPAKS which only run at 10G.

---


When using 10Base-T & 100Base-T , after autonegotiation fails and activates the parallel detection feature, the driver sets the interface to half-duplex. 

When autonegotiation fails on a 10/100 link, you will commonly see the collisions counter increase on the half-duplex side due to one side being 10/100 half while the other side is 10/100 full. On the half-duplex side the TX side is monitoring the RX side. If a packets is being received on the RX side the TX will wait until the frame is processed before transmitting again. In this scenario if the network is busy the half-duplex side may not have the chance to trasmit any frames if the full-duplex side is constantly trasmitting. 


# Configuring Autonegotiation

On cisco switches, autonegotiation is enabled by default. In order to set the duplex first the link must have a set speed.

You may configure the duplex for a Cisco switch using the command below 

`(config-if)#duplex [half|full|auto] `

You may configure the speed using the command below 

`(config-if)#speed [10|100|1000|auto]`



---


# VLANS

VLANS are configured to virtually separate a switch into distinct LAN's that treat each VLAN as if it were connected to its own physical switch. In theory a frame intended for one VLAN should not be able to reach the other VLANS. However , there are exploits to get around this. 

There are two known exploits for circumventing the virtual separation of VLAN's on a switch. The type of attack is dubbed "VLAN Hopping" and there are two methods to achieve this. 



## Switch spoofing 

In a switch spoofing attack , an attacker imitates a trunking switch by using the tagging and trunking protocols used for maintaining the VLAN. I will put a link to the two most common protocols used at the bottom of this paragraph. 

[Multiple VLAN Registration Protocol](https://en.wikipedia.org/wiki/Multiple_VLAN_Registration_Protocol)

[IEEE 802.1Q](https://en.wikipedia.org/wiki/IEEE_802.1Q)

[Dynamic Trunking Protocol](https://en.wikipedia.org/wiki/Dynamic_Trunking_Protocol)

Attackers take advantage of default configurations for DTP. Any switch port configured as DTP auto will receive DTP packets generated by the attackers device. Once the DTP packet is received by the DTP configured port on the switch , it will be configured as a trunk port and accept traffic for any VLAN on the trunk. This would then allow the attacker to send packets to any VLAN on the negotiated trunk.

## Double Tagging

Double tagging allows an attacker to send a frame to a destination VLAN other than the source VLAN. In a scenario , the attacker is currently on VLAN 10 and sends a packet with the outer header being set to "VLAN 10". The frame is forwarded by the first switch as "VLAN 10" matches the trunk port associated. Once the packet hits the 2nd switch the second header is read as "VLAN 20" and forwarded through all ports matching the "VLAN 20" ID in the inner header.

---

# Connecting VLANS

In order for a host on VLAN 1 to forward frames to host2 on VLAN 2 , an external router may be used to accomplish this. To do this , the router must be connected to a port on each VLAN in order to facilitate communication.

However, as a networks scales to add more switches , there is another solution for interconnecting VLAN's. To do this , you may use trunking. VLAN trunks are links that carry traffic for more than one VLAN.


Trunk ports are not assigned a VLAN. If you want to route traffic between all the VLAN's , you can use a "router-on-a-stick" approach , to do this you would connect a router to a trunk port on the switch. The only limitation with this setup is the bandwidth on the trunk port used. Often the port may be a 10 Mbps link which isn't optimal. Sometimes you will see a similar approach except instead of router you would use a firewall. Keep in mind this approach only applies to Layer 2 switches, layer 3 switches have a router built in which you could use to route traffic between all VLAN's.   


---

# Configuring VLAN's 


Some Cisco switch models like the 2950 and the 3550 come equiped with a VLAN database which has an entirely separate configuration than the switch. This was an older feature on Cisco switches. However newer models allow you to manage the VLAN's from the CLI 

---

# Trunking 

A trunk is an interface or link that can carry frames for more than one VLAN. Trunks are used to facilitate communication between two devices in the same VLAN on anoother switche. They can act as a metaphorical bridge to another switch's ports in the same VLAN. Trunking is not related to purely switches and can even be setup on routers / firewalls and other devices. 


When a switch receives a frame from a trunk port , it views the reference to the intended VLAN. This reference is stored in a header utilizing the Inter Switch Link( ISL) or 802.1Q protocol. Some swithes may not support the ISL protocol and may have to use the 802.1Q protocol. 

There are some differences between the two protocols and how they treat the ethernet frame. ISL encapsulates frames inside it's own ISL fram while 802.1Q alters the ethernet frame and adds a VLAN tag header 

You can see a picture of a 802.1Q frame below 

![packet](images/802.png)


---

# Proxies

- What is a Reverse Proxy ? 

* Forwards requests from servers to clients

* Hides IP of server.

* Reduce latency through caching

* Can compress outgoing requests , reducing bandwidth

* DDoS mitigation 

* Load balancing 

> Note: A reverse proxy is a proxy that sits between a client and a server. The reverse proxy forwards requests from a server to a client. 



- What is a Proxy?

> Note: A regular proxy sits between a client and server and forwards requests from the client to the server.


---


# SLIP : Serial line over IP 

* used in microcontrollers 
* replaced by PPP ( Point-to-Point protocol )
* Encapsulates data from serial ports and routers 
* small overhead compared to PPP



---


# NAT Reflection ( NAT Hairpinning )

* Allows hosts on a LAN to access each other through an external IP ( public IP , WAN IP ) 



---


# General networking concepts 

## Noob stuff 

* Network Segment : 

> Portion of a network where all devices are directly connected and can communicate with one another without needing a router. A LAN , for example, is a singular network segment. And by LAN this could be either a layer 2 LAN or layer 3 LAN. 


* What are Subnets ? : 

> Subnets are individual network segments that share a common broadcast domain within a larger network. 

* What are broadcast domains ? 
> broadcast domains are network segments where any broadcast sent by a device will be received by all devices in the same network segment 

* What's the difference between a layer 2 LAN and layer 3 LAN : 

> Layer 2 LAN's reference devices which can communicate and reference one another using MAC addresses while Layer 3 LAN's do the same but using IP addresses instead. 



## SPAN ( Switchport Port Analyzer )

* Allows you to copy the traffic from a singular port, group of ports,  or VLAN to a port where a monitoring device is connected  

* Can be used by packet capturing software 

* Allows you to observe traffic patterns 

* For VoIP , you can use span ports to monitor RTP streams and check for latency or jitter 


# Broadcast forwarding 

* Allows forwarding broadcast traffic from one network segment to another 


# DIA : Dedicated Internet Access :

* ISP's will run fiber from a router from their central office to your building 

* Guarantees on bandwidth , uptime , latency , jitter 


# Subnetting cheatsheet 

# IPv4 Subnet Table

| CIDR Notation | Subnet Mask     | Total Hosts | Usable Hosts | Usable IP Range                                  |
|---------------|-----------------|-------------|--------------|------------------------------------------------|
| /0            | 0.0.0.0         | 4,294,967,296 | 4,294,967,294 | 0.0.0.1 - 255.255.255.254                      |
| /1            | 128.0.0.0       | 2,147,483,648 | 2,147,483,646 | 0.0.0.1 - 127.255.255.254                      |
| /2            | 192.0.0.0       | 1,073,741,824 | 1,073,741,822 | 0.0.0.1 - 63.255.255.254                       |
| /3            | 224.0.0.0       | 536,870,912   | 536,870,910   | 0.0.0.1 - 31.255.255.254                       |
| /4            | 240.0.0.0       | 268,435,456   | 268,435,454   | 0.0.0.1 - 15.255.255.254                       |
| /5            | 248.0.0.0       | 134,217,728   | 134,217,726   | 0.0.0.1 - 7.255.255.254                        |
| /6            | 252.0.0.0       | 67,108,864    | 67,108,862    | 0.0.0.1 - 3.255.255.254                        |
| /7            | 254.0.0.0       | 33,554,432    | 33,554,430    | 0.0.0.1 - 1.255.255.254                        |
| /8            | 255.0.0.0       | 16,777,216    | 16,777,214    | 0.0.0.1 - 0.255.255.254                        |
| /9            | 255.128.0.0     | 8,388,608     | 8,388,606     | 0.0.0.1 - 0.127.255.254                        |
| /10           | 255.192.0.0     | 4,194,304     | 4,194,302     | 0.0.0.1 - 0.63.255.254                         |
| /11           | 255.224.0.0     | 2,097,152     | 2,097,150     | 0.0.0.1 - 0.31.255.254                         |
| /12           | 255.240.0.0     | 1,048,576     | 1,048,574     | 0.0.0.1 - 0.15.255.254                         |
| /13           | 255.248.0.0     | 524,288       | 524,286       | 0.0.0.1 - 0.7.255.254                          |
| /14           | 255.252.0.0     | 262,144       | 262,142       | 0.0.0.1 - 0.3.255.254                          |
| /15           | 255.254.0.0     | 131,072       | 131,070       | 0.0.0.1 - 0.1.255.254                          |
| /16           | 255.255.0.0     | 65,536        | 65,534        | 0.0.0.1 - 0.0.255.254                          |
| /17           | 255.255.128.0   | 32,768        | 32,766        | 0.0.0.1 - 0.0.127.254                          |
| /18           | 255.255.192.0   | 16,384        | 16,382        | 0.0.0.1 - 0.0.63.254                           |
| /19           | 255.255.224.0   | 8,192         | 8,190         | 0.0.0.1 - 0.0.31.254                           |
| /20           | 255.255.240.0   | 4,096         | 4,094         | 0.0.0.1 - 0.0.15.254                           |
| /21           | 255.255.248.0   | 2,048         | 2,046         | 0.0.0.1 - 0.0.7.254                            |
| /22           | 255.255.252.0   | 1,024         | 1,022         | 0.0.0.1 - 0.0.3.254                            |
| /23           | 255.255.254.0   | 512           | 510           | 0.0.0.1 - 0.0.1.254                            |
| /24           | 255.255.255.0   | 256           | 254           | 0.0.0.1 - 0.0.0.254                            |
| /25           | 255.255.255.128 | 128           | 126           | 0.0.0.1 - 0.0.0.126                            |
| /26           | 255.255.255.192 | 64            | 62            | 0.0.0.1 - 0.0.0.62                             |
| /27           | 255.255.255.224 | 32            | 30            | 0.0.0.1 - 0.0.0.30                             |
| /28           | 255.255.255.240 | 16            | 14            | 0.0.0.1 - 0.0.0.14                             |
| /29           | 255.255.255.248 | 8             | 6             | 0.0.0.1 - 0.0.0.6                              |
| /30           | 255.255.255.252 | 4             | 2             | 0.0.0.1 - 0.0.0.2                              |
| /31           | 255.255.255.254 | 2             | 0             | Point-to-Point Only                            |
| /32           | 255.255.255.255 | 1             | 0             | Single Host Only                               |



# DNS cheatsheet 


## Records 

A -> maps FQDN to IPv4 address 

AAAA -> maps FQDN to IPv6 address 

SOA -> Start of Authority records stores info about domains and it's used to direct how a dns zone propagates to secondary name servers.  

NS -> Name Server  , specifies which servers are authoritive for a domain or sub-domain , should not be pointed to CNAME.  

PTR -> Pointer record, maps IP addresses to hostnames 

SRV -> records used for Instant messaging or Voip , directed to separate host and port location 

TXT -> Allows admins to add additional info such as site ownership and email validation 

CNAME -> Record that acts like an alias , pionts to another domain or a subdomain , never points to an IP address. Allows you to make changes to the IP a domain points to without affecting bookmarks  


