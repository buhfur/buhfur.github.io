---
title: Wake on LAN setup
parent: Nmcli Cheatsheet
grand_parent: Linux
nav_order: 40
layout: default
---
# Table of Contents  
{: .no_toc }

1. TOC 
{:toc}

---


# nmcli Cheatsheet (Simple → Advanced)

> A practical, copy‑paste oriented guide to NetworkManager’s CLI. Starts with read‑only inspection, moves through day‑to‑day tasks, and ends with advanced profiles and multi‑NIC setups. No tables, just headings and code blocks.

## Basics

### Version, help, overview
```bash
nmcli --version
nmcli help
nmcli general status
nmcli -p general status          # pretty output
nmcli -t -f all general status   # terse output with all fields
nmcli monitor                    # watch connectivity changes live
```

### Devices and connections
```bash
nmcli device status
nmcli device show
nmcli device show eth0
nmcli connection show
nmcli connection show --active
nmcli -f name,uuid,type,device connection show
```

### Enable/disable networking & radios
```bash
nmcli networking on
nmcli networking off

nmcli radio all off
nmcli radio wifi off
nmcli radio wifi on
nmcli radio wwan on
```

### Bring links up/down; reconnect
```bash
nmcli device disconnect eth0
nmcli device connect eth0
nmcli device reapply eth0         # re-apply profile to running device
nmcli connection up "Wired connection 1"
nmcli connection down "Wired connection 1"
nmcli connection reload           # reload profiles from disk
```

### Rescan Wi‑Fi and list APs
```bash
nmcli device wifi rescan
nmcli device wifi list
nmcli -f ssid,chan,rate,signal,security device wifi list
```

## Everyday configuration

### DHCP IPv4 on Ethernet
```bash
nmcli connection add type ethernet ifname eth0 con-name eth0-dhcp
# or convert an existing profile to DHCP
nmcli connection modify eth0 ipv4.method auto ipv6.method ignore
nmcli connection up eth0
```

### Static IPv4 on Ethernet
```bash
nmcli connection add type ethernet ifname eth0 con-name eth0-static   ipv4.method manual ipv4.addresses 192.0.2.10/24 ipv4.gateway 192.0.2.1   ipv4.dns "9.9.9.9 1.1.1.1" ipv4.dns-search "example.com"
nmcli connection up eth0-static
```

### Static IPv6
```bash
nmcli connection modify eth0-static   ipv6.method manual ipv6.addresses 2001:db8::10/64   ipv6.gateway 2001:db8::1   ipv6.dns "2606:4700:4700::1111 2620:fe::9"   ipv6.addr-gen-mode eui64
nmcli connection up eth0-static
```

### Disable IPv6 (per‑profile) or IPv4
```bash
nmcli connection modify eth0-static ipv6.method ignore
nmcli connection modify eth0-static ipv4.method disabled
```

### Set MTU and link options
```bash
nmcli connection modify eth0-static 802-3-ethernet.mtu 9000
nmcli device reapply eth0
```

### Set metrics & routing
```bash
nmcli connection modify eth0-static ipv4.route-metric 100
nmcli connection modify eth0-static +ipv4.routes "198.51.100.0/24 192.0.2.1 50"
nmcli connection modify eth0-static +ipv6.routes "2001:db8:100::/64 2001:db8::1 100"
nmcli connection up eth0-static
```

### Split DNS and per‑family DNS
```bash
nmcli connection modify eth0-static ipv4.ignore-auto-dns yes
nmcli connection modify eth0-static ipv4.dns "10.0.0.53 10.0.0.54"
nmcli connection modify eth0-static ipv4.dns-search "corp.example com.example"
nmcli connection modify eth0-static ipv6.ignore-auto-dns yes
nmcli connection modify eth0-static ipv6.dns "2001:db8::53"
nmcli connection up eth0-static
```

### Autoconnect and metered
```bash
nmcli connection modify eth0-static connection.autoconnect yes
nmcli connection modify eth0-static connection.autoconnect-priority 10
nmcli connection modify wwan0 connection.metered yes
```

### Mark a device managed/unmanaged by NM
```bash
nmcli device set eth0 managed yes
nmcli device set eth0 managed no
```

## Wi‑Fi

### Connect to a WPA/WPA2/WPA3 PSK network
```bash
nmcli device wifi connect "MySSID" password "supersecret"
# create a named profile bound to wlan0
nmcli connection add type wifi ifname wlan0 con-name home-wifi ssid "MySSID"
nmcli connection modify home-wifi wifi-sec.key-mgmt wpa-psk wifi-sec.psk "supersecret"
nmcli connection up home-wifi
```

### Hidden SSID
```bash
nmcli device wifi connect "HiddenSSID" password "pass" hidden yes
```

### 802.1X Enterprise (PEAP/MSCHAPv2 example)
```bash
nmcli connection add type wifi ifname wlan0 con-name corp-wifi ssid "CorpNet"
nmcli connection modify corp-wifi   802-1x.eap peap 802-1x.identity "user@example.com"   802-1x.phase2-auth mschapv2 802-1x.password "supersecret"   wifi-sec.key-mgmt wpa-eap
nmcli connection up corp-wifi
```

### Wi‑Fi hotspot (NAT/shared)
```bash
nmcli connection add type wifi ifname wlan0 con-name hotspot ssid "MyHotspot"   ipv4.method shared ipv6.method ignore
nmcli connection modify hotspot wifi.band bg wifi.channel 6   wifi-sec.key-mgmt wpa-psk wifi-sec.psk "changeme123"
nmcli connection up hotspot
```

## VLANs, Bonds, Bridges

### VLAN on a physical NIC
```bash
nmcli connection add type vlan ifname eth0.20 dev eth0 id 20 con-name vlan20
nmcli connection modify vlan20 ipv4.method manual ipv4.addresses 192.0.2.20/24
nmcli connection up vlan20
```

### Bond (LACP 802.3ad) with slave ports
```bash
nmcli connection add type bond ifname bond0 mode 802.3ad miimon 100 lacp-rate fast
nmcli connection add type ethernet ifname eth1 master bond0
nmcli connection add type ethernet ifname eth2 master bond0
nmcli connection modify bond0 ipv4.method auto
nmcli connection up bond0
```

### Bridge with ports
```bash
nmcli connection add type bridge ifname br0 stp yes
nmcli connection add type ethernet ifname eth3 master br0
nmcli connection add type vlan ifname vlan20 dev eth0 id 20 master br0
nmcli connection modify br0 ipv4.method manual ipv4.addresses 192.0.2.30/24
nmcli connection up br0
```

## WireGuard and VPNs

### WireGuard interface (basic)
```bash
nmcli connection add type wireguard ifname wg0 con-name wg0
nmcli connection modify wg0 ipv4.addresses 10.10.0.2/24 ipv4.method manual
nmcli connection modify wg0 wireguard.private-key "$(cat /etc/wireguard/privatekey)"
nmcli connection modify wg0 +wireguard.peers.public-key "BASE64PUBKEY=="
nmcli connection modify wg0 +wireguard.peers.endpoint "vpn.example.com:51820"
nmcli connection modify wg0 +wireguard.peers.allowed-ips "10.10.0.0/24,192.168.50.0/24"
nmcli connection up wg0
```

### Import an OpenVPN config
```bash
nmcli connection import type openvpn file /path/to/client.ovpn
nmcli connection up <imported-name>
```

### Strongswan/IPsec profile (if plugin installed)
```bash
nmcli connection add type vpn con-name site-a ifname -- vpn-type strongswan   vpn.data "address=vpn.example.com,method=psk,ipsec-interface=0"
nmcli connection up site-a
```

## Policy routing and advanced IP

### Additional IPs, metrics, and PBR rules
```bash
nmcli connection modify eth0-static +ipv4.addresses 192.0.2.11/24
nmcli connection modify eth0-static ipv4.route-table 100
nmcli connection modify eth0-static +ipv4.routes "0.0.0.0/0 192.0.2.1 100 table=100"
nmcli connection modify eth0-static +ipv4.routing-rules "priority 100 from 192.0.2.11 table 100"
nmcli connection up eth0-static
```

### Disable reverse path filtering for a profile (example)
```bash
nmcli connection modify eth0-static ipv4.never-default yes
nmcli connection up eth0-static
```

## DNS options and resolution

### DNS priority and per‑connection ordering
```bash
nmcli connection modify eth0-static ipv4.dns-priority -10
nmcli connection modify wifi-home ipv4.dns-priority 100
nmcli connection up eth0-static
```

### Switch to systemd‑resolved integration (if applicable)
```bash
nmcli connection modify eth0-static ipv4.dns "1.1.1.1 9.9.9.9"
nmcli connection modify eth0-static ipv4.dns-search "example.com"
nmcli connection up eth0-static
```

## Security, MAC, and autoconnect behavior

### MAC randomization and cloning
```bash
nmcli connection modify wifi-home wifi.mac-address-randomization yes
nmcli connection modify wifi-home 802-11-wireless.cloned-mac-address "12:34:56:00:00:01"
nmcli connection up wifi-home
```

### Disable autoconnect temporarily
```bash
nmcli connection modify wifi-home connection.autoconnect no
nmcli connection down wifi-home
```

## Connection lifecycle

### Rename, duplicate, delete
```bash
nmcli connection modify eth0-static connection.id "prod-eth0"
nmcli connection clone prod-eth0 prod-eth0-backup
nmcli connection delete prod-eth0-backup
```

### Export/import keyfile
```bash
nmcli connection export prod-eth0 /etc/NetworkManager/system-connections/prod-eth0.nmconnection
nmcli connection import type ethernet file /etc/NetworkManager/system-connections/prod-eth0.nmconnection
```

## Diagnostics and logging

### Show properties and active settings
```bash
nmcli -f all connection show prod-eth0
nmcli -f GENERAL,IP4,IP6 device show eth0
nmcli general hostname
nmcli -g ip4.address device show eth0
```

### Connectivity check
```bash
nmcli networking connectivity check
nmcli networking connectivity
```

### Increase NM logging temporarily
```bash
nmcli general logging level DEBUG domains ALL
# revert (example)
nmcli general logging level INFO domains DEFAULT
```

## Tethering and shared connections

### Share Ethernet via another NIC
```bash
nmcli connection add type ethernet ifname enxUSB con-name share-lan   ipv4.method shared ipv6.method ignore
nmcli connection up share-lan
```

## Miscellaneous niceties

### Make a one‑liner profile with multiple properties
```bash
nmcli connection add type ethernet ifname eth0 con-name quick-static   ipv4.method manual ipv4.addresses 192.0.2.44/24 ipv4.gateway 192.0.2.1   ipv4.dns "9.9.9.9 1.1.1.1" ipv4.route-metric 50 connection.autoconnect yes
```

### Key paths and reloads
```bash
# Profiles live (by default) in:
# /etc/NetworkManager/system-connections/*.nmconnection
nmcli connection reload
systemctl reload NetworkManager
```

## Quick recipes

### Two uplinks with preferred primary
```bash
nmcli connection modify eth0 ipv4.method auto ipv4.route-metric 10
nmcli connection modify eth1 ipv4.method auto ipv4.route-metric 100
nmcli connection up eth0 && nmcli connection up eth1
```

### Failover Wi‑Fi then Ethernet
```bash
nmcli connection modify wifi-home connection.autoconnect-priority 100
nmcli connection modify eth0     connection.autoconnect-priority 50
```

### Replace DHCP DNS with custom ones (keep DHCP IP/gw)
```bash
nmcli connection modify eth0 ipv4.ignore-auto-dns yes
nmcli connection modify eth0 ipv4.dns "10.1.1.53 10.1.1.54"
nmcli connection up eth0
```

---

## Useful field names (for -f and -g)
```bash
GENERAL,GENERAL.STATE,GENERAL.CONNECTION,GENERAL.DEVICE
IP4,IP4.ADDRESS,IP4.GATEWAY,IP4.DNS,IP4.DOMAIN,IP4.ROUTE,IP4.METHOD
IP6,IP6.ADDRESS,IP6.GATEWAY,IP6.DNS,IP6.DOMAIN,IP6.ROUTE,IP6.METHOD
CONNECTION,CONNECTION.ID,CONNECTION.UUID,CONNECTION.TYPE,CONNECTION.INTERFACE-NAME
WIFI,802-11-wireless.ssid,802-11-wireless.mode,802-11-wireless.band
WIFI-SEC,802-11-wireless-security.key-mgmt,802-11-wireless-security.psk
ETHERNET,802-3-ethernet.mtu,802-3-ethernet.mac-address
BRIDGE,BOND,VLAN,VPN,WIRED-PROPERTIES,WIREGUARD
```

---

### Notes
- Some features require the corresponding NetworkManager plugin (e.g., OpenVPN, Strongswan/IPsec).
- Property names are case‑sensitive and use dot notation as shown in `nmcli connection show <name>` output.
- After `modify`, use `nmcli device reapply <ifname>` or `nmcli connection up <name>` to push changes.
