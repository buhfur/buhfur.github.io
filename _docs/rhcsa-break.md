---

layout: default
title: "RHEL VM DESTRUCTION"

---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

In order to get more practice studying for RHCSA , i've decided to cobble together a few tactics that will force me to stop , think , and fix various things on my VM. I've done this as it's easy to coast along doing the same repetitive tasks while studying hammer commands & snippets. This however is a chance to break outside of the repition and force me to apply the knowledge and fix a very broken VM. This is not for the faint of heart, you have been warned.


## 1. Changed root password to something random

Setup root password as something random , nuff said

## 2. Wiped all configurations and connections in NetworkManager

```bash
sudo rm -rf /etc/NetworkManager/system-connections/*
```

## 3. Corrupted Bootloader entry 

1. Changed default systemd unit to boot into getty.target 

## 4. Disabled sshd daemon 

```bash
systemctl disable sshd
```

## 5. Block ssh in firewall 

```bash
firewall-cmd --permanent --add-rich-rule='rule family=ipv4 source address=0.0.0.0/0 service name=ssh drop'
firewall-cmd --reload

```

## 6. Masked firewalld systemd unit 

```bash
systemctl mask firewalld.service
```

