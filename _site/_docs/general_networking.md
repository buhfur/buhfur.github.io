

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
