---
title: Cisco Switch/Router Snippets & Notes
parent: Cisco
nav_order: 10
layout: default
---
# TOC
{:.no_toc}

1. TOC 
{:toc}

# General Snippets
---

---
title: Cisco Troubleshooting Command Cheat Sheet
nav_order: 1
has_children: false
parent: Network Troubleshooting
---

# Cisco Troubleshooting Command Cheat Sheet

## 1. Interface and Physical Layer

- Show interface status and protocol:  
  `show interfaces status`
- Detailed interface statistics:  
  `show interfaces [interface]`
- Check interface errors:  
  `show interfaces [interface] | include error`
- Show interface description:  
  `show interfaces description`
- Display operational speed/duplex:  
  `show controllers [interface]`
- Verify interface transceiver info (SFP):  
  `show hw-module interface [interface] transceiver`
- Reset interface:  
  `shutdown` / `no shutdown`
- Clear interface counters:  
  `clear counters [interface]`

---

## 2. Layer 2 Troubleshooting

- Display VLAN information:  
  `show vlan brief`
- Display spanning-tree status:  
  `show spanning-tree`
- Show port security status:  
  `show port-security interface [interface]`
- Check MAC address table:  
  `show mac address-table`
- Display trunk links:  
  `show interfaces trunk`
- Show LLDP neighbors:  
  `show lldp neighbors`  
  `show lldp neighbors detail`
- Show CDP neighbors:  
  `show cdp neighbors`  
  `show cdp neighbors detail`
- Verify STP root bridge:  
  `show spanning-tree root`

---

## 3. IP Connectivity

- Show IP interface summary:  
  `show ip interface brief`
- Detailed IP interface info:  
  `show ip interface [interface]`
- Ping with specific parameters:  
  `ping [destination] source [interface/IP] repeat 10`
- Traceroute to destination:  
  `traceroute [destination]`

> Note: Hit "Ctrl + Shift + 6" to cancel traceroute 
- Display ARP table:  
  `show ip arp`
- Clear ARP cache:  
  `clear arp-cache`
- Show routing table:  
  `show ip route`
- Show IP routing protocol summary:  
  `show ip protocols`

---

## 4. BGP Troubleshooting

- Show BGP summary:  
  `show ip bgp summary`
- Show BGP neighbors:  
  `show ip bgp neighbors`
- Show BGP advertised routes:  
  `show ip bgp neighbors [neighbor IP] advertised-routes`
- Show BGP received routes:  
  `show ip bgp neighbors [neighbor IP] received-routes`
- Show specific BGP route:  
  `show ip bgp [prefix]`
- Check BGP routing table for prefix:  
  `show ip route [prefix]`
- Clear BGP sessions (use with caution):  
  `clear ip bgp *`
- Debug BGP events:  
  `debug ip bgp events`
- Debug BGP updates:  
  `debug ip bgp updates`
- View BGP flap dampening info:  
  `show ip bgp dampening`

---

## 5. IPsec / VPN Troubleshooting

- Show crypto ISAKMP SA (IKE Phase 1):  
  `show crypto isakmp sa`
- Show crypto IPsec SA (Phase 2):  
  `show crypto ipsec sa`
- Show crypto map configuration:  
  `show crypto map`
- Show crypto session details:  
  `show crypto session detail`
- Debug IKE negotiation:  
  `debug crypto isakmp`
- Debug IPsec negotiations:  
  `debug crypto ipsec`
- Show VPN peer status:  
  `show crypto session`
- Verify NAT exemption ACLs:  
  `show access-lists`
- Check tunnel interface status:  
  `show interfaces tunnel [number]`
- Reset IKE/IPsec SAs:  
  `clear crypto sa` / `clear crypto isakmp sa`

---

## 6. Routing Protocols (General)

- Show routing protocol summary:  
  `show ip protocols`
- Show EIGRP neighbors:  
  `show ip eigrp neighbors`
- Show OSPF neighbors:  
  `show ip ospf neighbor`
- Show OSPF interfaces:  
  `show ip ospf interface`
- Show OSPF database:  
  `show ip ospf database`
- Display route redistribution:  
  `show run | include redistribute`

---

## 7. Tunnel and GRE Troubleshooting

- Show tunnel interface:  
  `show interfaces tunnel [number]`
- Check tunnel source/destination:  
  `show run interface tunnel [number]`
- Check tunnel status:  
  `show ip interface brief | include Tunnel`
- Ping using tunnel source:  
  `ping [destination] source [tunnel interface]`
- Debug tunnel encapsulation:  
  `debug tunnel`
- Check routing over tunnel:  
  `show ip route | include [tunnel network]`

---

## 8. CPU, Memory, and Process Monitoring

- Show CPU utilization:  
  `show processes cpu sorted`
- Show memory usage:  
  `show processes memory sorted`
- Show platform hardware resources:  
  `show platform resources`
- Show process usage per feature:  
  `show processes cpu history`

---

## 9. Logging and Debugging

- View system log messages:  
  `show logging`
- Set terminal logging:  
  `terminal monitor`
- Disable terminal logging:  
  `terminal no monitor`
- Show debug status:  
  `show debug`
- Disable all debugging:  
  `undebug all` / `no debug all`
- Save running config logs:  
  `copy running-config startup-config`
- Display clock for log correlation:  
  `show clock`

---

## 10. Miscellaneous

- Show version and uptime:  
  `show version`
- Show configuration changes:  
  `show archive config differences`
- Show running configuration:  
  `show running-config`
- Show startup configuration:  
  `show startup-config`
- Save configuration:  
  `copy running-config startup-config`
- Show inventory:  
  `show inventory`
- Show license information:  
  `show license`

---

## Notes

- Always confirm before using `clear` or `debug` commands in production.
- Use output filters to refine results:
  - `| include`
  - `| exclude`
  - `| section`
- Disable paging for long outputs:  
  `terminal length 0`


# General notes 
---


## Enhanced Interior Gateway Routing (EIGRP)

* allows router to share routes with other routers within AS'es (autonomous systems)



## Virtual Routing & Forwarding 

* used to facilitate mpls 

* VRF-lite is VRF without MPLS 

* used by service providers to allow one device to carry traffic from multiple customers 

* Allows you to split physical router into multiple virtual routers , "vlans for routers"

* Separate routing tables for each instance 

* TCP / IP layer 3 interfaces can be configured to use VRF  

* interfaces and routes are configured to a specified VRF

* cannot be applied to layer 2 interfaces

* traffic in one VRF cannot be forwarded out of an interface in another VRF without VRF leaking 

* Router intefaces , SVI's and routed ports on multilayered switches can be configured in a VRF  

## Generic Routing Encapsulation(GRE)

- Layer 2 adjacency between two routers separated by cloud 
- IPSec implements encryption , GRE does not implement encryption by default 
- Encapsulation in GRE packets , both multicast or unicast 
- Pipe GRE packets into IPSec tunnel 
- GRE tunnels creates a virtual link and as a consequence , lowers the hop count  

