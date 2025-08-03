
---

layout: default
title: "Wake on LAN setup"

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


## Router configuration 

Verify your router doesn't block broadcasts 

