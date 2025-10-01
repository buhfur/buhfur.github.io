---
title: Wake on LAN setup
parent: Networking
grand_parent: Linux
nav_order: 30
layout: default
---
# Table of Contents  
{: .no_toc }

1. TOC 
{:toc}

---


# Pre-configuration 

Make sure your ethernet interface supports it 
```bash
ethtool enoX
```

Output should look like below
```bash
Supports Wake-on: pumbg
Wake-on: g
```

If "Wake-on" doesn't show "g" , that means you need to enable wake on lan for the interface. You will also need to configure your BIOS to allow WOL if your NIC supports it.   

## Enable WOL temporarily 

If "Wake-on" shows "d" , do the following command below to enable WOL on the interface. Replace "X" with the number of your ethernet interface 
```bash
ethtool -s enoX wol g
```

## WOL systemd unit 

The command above enables WOL temporarilly ( for testing ) , to make this change persistent, create the following systemd unit 
```bash
[Unit]
Description=Enable Wake-on-LAN
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/ethtool -s eno1 wol g

[Install]
WantedBy=multi-user.target
```

Then run 
```bash
systemctl daemon-reexec
systemctl enable wol.service
```


## Local Network Configuration 

Make sure your router or firewall doesn't drop packets. I did this by enabling verbose logging for broadcast packets on firewalld 
```bash
firewall-cmd --set-log-denied=broadcast && firewall-cmd --reload
```

Also enable broadcast pings on machine 
```bash
sudo sysctl -w net.ipv4.icmp_echo_ignore_broadcasts=0
```

Then on another host , use a WOL client ( your choice , for this i'm using wol on RHEL even though the host to be woken up is debian ).

On host 1 , look for incoming packets on UDP port 9 ( default WOL port )
```bash
tcpdump -i eno2 udp port 9
```

Get host 2's MAC address and send wake command ( one liner cause why not  )
```bash
wol $( arp -n 192.168.1.2 | awk -F " " '{print $3}' | tail -n1) 
```
