---

layout: default
title: "Cisco Switch/Router Snippets & Notes"

---

# TOC
{:.no_toc}

1. TOC 
{:toc}

# General Snippets
---

Get time/timezone
    ```bash
    show clock
    ```

Show all VRF Interfaces 
    ```bash
    show vrf 

    show ip vrf 
    ```
Ping IP through VRF interface 
    ```bash
    ping vrf <vrf-name> <ip> 
    ```

Filter logging 
    ```bash
    sh logging | include May 16 
    ```


Traceroute
    ```bash
    traceroute 8.8.8.8 source GigabitEthernet0/0
    traceroute 8.8.8.8 source 192.0.2.1
    ```

EIGRP Neighbors 
    ```bash
    sh ip eigrp nei 
    ```

IPSec Tunnel info 
    ```bash
    sh crypto session detail 
    sh crypto session brief 
    ```

IKE Phase 1 
    ```bash
    show crypto isakmp sa detail      (IKEv1)
    show crypto ikev2 sa detail       (IKEv2)
    ```
Check IPSec phase 2 status 
    ```bash
    show crypto ipsec sa
    ```
# General notes 
---

## Virtual Routing & Forwarding 

- used to facilitate MPLS 
- VRF-lite is VRF without MPLS 
- used by service providers to allow one device to carry traffic from multiple customers 
- Allows you to split physical router into multiple virtual routers , "vlans for routers"
- Separate routing tables for each instance 
- TCP / IP layer 3 interfaces can be configured to use VRF  
- interfaces and routes are configured to a specified VRF
- cannot be applied to layer 2 interfaces
- traffic in one VRF cannot be forwarded out of an interface in another VRF without VRF leaking 
- Router intefaces , SVI's and routed ports on multilayered switches can be configured in a VRF  

## Generic Routing Encapsulation 

- Layer 2 adjacency between two routers separated by cloud 
- IPSec implements encryption , GRE does not implement encryption by default 
- Encapsulation in GRE packets , both multicast or unicast 
- Pipe GRE packets into IPSec tunnel 
- GRE tunnels creates a virtual link and as a consequence , lowers the hop count  

