---
title: Basic Networking Tools
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

# Linux Networking Tools & Interface Configuration Snippets

Practical wiki reference for Linux network interface configuration and troubleshooting on Debian/Ubuntu and Red Hat/RHEL-based systems.

Applies broadly to Debian, Ubuntu, RHEL, Rocky Linux, AlmaLinux, CentOS Stream, and Debian-based virtualization hosts such as Proxmox.

---

## Quick Checks

### Show interfaces

```bash
ip link show
```

### Show IP addresses

```bash
ip addr show
```

Short version:

```bash
ip a
```

### Show one interface

```bash
ip addr show dev ens18
```

### Show routes

```bash
ip route
```

### Show default route

```bash
ip route show default
```

### Check route used for a destination

```bash
ip route get 8.8.8.8
```

### Show listening ports

```bash
ss -tulpn
```

### Show established connections

```bash
ss -tunp
```

---

## Identify Interface Names

Modern systems usually use names like:

```text
ens18
enp1s0
eno1
eth0
```

### List interface names only

```bash
ip -o link show | awk -F': ' '{print $2}'
```

### Match interface to MAC address

```bash
ip link show
```

### Show hardware details

Debian / Ubuntu:

```bash
sudo apt update
sudo apt install lshw
sudo lshw -class network
```

Red Hat / RHEL:

```bash
sudo dnf install lshw
sudo lshw -class network
```

---

## Temporary Interface Changes

These changes do not survive reboot unless added to the permanent config.

### Bring interface up

```bash
sudo ip link set ens18 up
```

### Bring interface down

```bash
sudo ip link set ens18 down
```

### Add temporary IP

```bash
sudo ip addr add 192.168.1.50/24 dev ens18
```

### Remove temporary IP

```bash
sudo ip addr del 192.168.1.50/24 dev ens18
```

### Add default gateway

```bash
sudo ip route add default via 192.168.1.1
```

### Replace default gateway

```bash
sudo ip route replace default via 192.168.1.1
```

### Delete default gateway

```bash
sudo ip route del default
```

---

# Debian / Ubuntu Networking

Debian and Ubuntu can use several network configuration systems:

- `/etc/network/interfaces`
- `ifupdown`
- `ifupdown2`
- Netplan
- NetworkManager

Proxmox commonly uses `/etc/network/interfaces` with `ifupdown2`.

---

## Debian: `/etc/network/interfaces`

### Static IP

Edit:

```bash
sudo nano /etc/network/interfaces
```

Example:

```ini
auto ens18
iface ens18 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 8.8.8.8
```

Apply:

```bash
sudo ifreload -a
```

If `ifreload` is missing:

```bash
sudo apt update
sudo apt install ifupdown2
```

Alternative:

```bash
sudo ifdown ens18 && sudo ifup ens18
```

Warning: using `ifdown` over SSH can disconnect your session.

---

### DHCP

```ini
auto ens18
iface ens18 inet dhcp
```

Apply:

```bash
sudo ifreload -a
```

---

### Multiple IPs on one interface

```ini
auto ens18
iface ens18 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 8.8.8.8

iface ens18 inet static
    address 192.168.1.51/24
```

Alternative using hooks:

```ini
auto ens18
iface ens18 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    post-up ip addr add 192.168.1.51/24 dev ens18
    pre-down ip addr del 192.168.1.51/24 dev ens18
```

---

## Debian: ifreload / ifupdown2

`ifreload` is provided by `ifupdown2`.

### Install ifupdown2

```bash
sudo apt update
sudo apt install ifupdown2
```

### Reload all interfaces

```bash
sudo ifreload -a
```

### Reload one interface

```bash
sudo ifreload ens18
```

### Check interface config

```bash
sudo ifquery --check ens18
```

### Show parsed config

```bash
ifquery ens18
```

### List known interfaces

```bash
ifquery --list
```

### Bring interface up

```bash
sudo ifup ens18
```

### Bring interface down

```bash
sudo ifdown ens18
```

---

## Ubuntu: Netplan

Ubuntu Server often uses Netplan.

### Find Netplan configs

```bash
ls -l /etc/netplan/
```

Common file names:

```text
00-installer-config.yaml
01-netcfg.yaml
50-cloud-init.yaml
```

### Static IP

```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: false
      addresses:
        - 192.168.1.50/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```

Test safely:

```bash
sudo netplan try
```

Apply:

```bash
sudo netplan apply
```

### DHCP

```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: true
```

Validate syntax:

```bash
sudo netplan generate
```

Apply:

```bash
sudo netplan apply
```

---

## Debian / Ubuntu Restart Commands

Use the command that matches the active networking stack.

```bash
sudo ifreload -a
```

```bash
sudo systemctl restart networking
```

```bash
sudo netplan apply
```

```bash
sudo systemctl restart NetworkManager
```

---

# Red Hat / RHEL Networking

RHEL-based systems commonly use NetworkManager.

Main tools:

- `nmcli`
- `nmtui`
- `/etc/NetworkManager/system-connections/*.nmconnection`

Older systems may use:

- `/etc/sysconfig/network-scripts/ifcfg-*`

---

## NetworkManager Checks

### Status

```bash
nmcli general status
```

### Devices

```bash
nmcli device status
```

### Connections

```bash
nmcli connection show
```

Short version:

```bash
nmcli con show
```

### Active connections

```bash
nmcli con show --active
```

### Device details

```bash
nmcli dev show ens18
```

---

## RHEL: Static IP with nmcli

Find the connection name:

```bash
nmcli con show
```

Example connection name:

```text
Wired connection 1
```

Set static IP:

```bash
sudo nmcli con mod "Wired connection 1" \
  ipv4.addresses 192.168.1.50/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "1.1.1.1 8.8.8.8" \
  ipv4.method manual
```

Apply:

```bash
sudo nmcli con down "Wired connection 1"
sudo nmcli con up "Wired connection 1"
```

Less disruptive option:

```bash
sudo nmcli device reapply ens18
```

---

## RHEL: DHCP with nmcli

```bash
sudo nmcli con mod "Wired connection 1" ipv4.method auto
sudo nmcli con down "Wired connection 1"
sudo nmcli con up "Wired connection 1"
```

---

## RHEL: Create New Static Connection

```bash
sudo nmcli con add type ethernet ifname ens18 con-name ens18-static \
  ipv4.addresses 192.168.1.50/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "1.1.1.1 8.8.8.8" \
  ipv4.method manual
```

Bring it up:

```bash
sudo nmcli con up ens18-static
```

---

## RHEL: Interactive Network Config

```bash
sudo nmtui
```

Useful when you want a terminal UI instead of long `nmcli` commands.

---

## RHEL: Connection Files

NetworkManager connection files are usually stored here:

```bash
/etc/NetworkManager/system-connections/
```

List them:

```bash
sudo ls -l /etc/NetworkManager/system-connections/
```

After manually editing a connection file:

```bash
sudo nmcli connection reload
sudo nmcli con up CONNECTION_NAME
```

---

# DNS Configuration

## Check DNS resolver

```bash
cat /etc/resolv.conf
```

## systemd-resolved status

```bash
resolvectl status
```

## DNS lookup

```bash
dig example.com
```

Install `dig`:

Debian / Ubuntu:

```bash
sudo apt install dnsutils
```

Red Hat / RHEL:

```bash
sudo dnf install bind-utils
```

## Query a specific DNS server

```bash
dig @1.1.1.1 example.com
```

## Resolver through system NSS

```bash
getent hosts example.com
```

---

## Debian DNS in `/etc/network/interfaces`

```ini
auto ens18
iface ens18 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 8.8.8.8
    dns-search example.local
```

Apply:

```bash
sudo ifreload -a
```

---

## Netplan DNS

```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: false
      addresses:
        - 192.168.1.50/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        search:
          - example.local
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```

Apply:

```bash
sudo netplan apply
```

---

## RHEL DNS with nmcli

```bash
sudo nmcli con mod CONNECTION_NAME ipv4.dns "1.1.1.1 8.8.8.8"
sudo nmcli con mod CONNECTION_NAME ipv4.dns-search "example.local"
sudo nmcli con up CONNECTION_NAME
```

Ignore DNS from DHCP:

```bash
sudo nmcli con mod CONNECTION_NAME ipv4.ignore-auto-dns yes
sudo nmcli con up CONNECTION_NAME
```

---

# Routing

## Add static route temporarily

```bash
sudo ip route add 10.10.20.0/24 via 192.168.1.254
```

## Delete static route temporarily

```bash
sudo ip route del 10.10.20.0/24
```

---

## Debian static route

```ini
auto ens18
iface ens18 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    post-up ip route add 10.10.20.0/24 via 192.168.1.254
    pre-down ip route del 10.10.20.0/24 via 192.168.1.254
```

Apply:

```bash
sudo ifreload -a
```

---

## Netplan static route

```yaml
network:
  version: 2
  ethernets:
    ens18:
      addresses:
        - 192.168.1.50/24
      routes:
        - to: default
          via: 192.168.1.1
        - to: 10.10.20.0/24
          via: 192.168.1.254
      nameservers:
        addresses:
          - 1.1.1.1
```

Apply:

```bash
sudo netplan apply
```

---

## RHEL static route with nmcli

Add:

```bash
sudo nmcli con mod CONNECTION_NAME +ipv4.routes "10.10.20.0/24 192.168.1.254"
sudo nmcli con up CONNECTION_NAME
```

Remove:

```bash
sudo nmcli con mod CONNECTION_NAME -ipv4.routes "10.10.20.0/24 192.168.1.254"
sudo nmcli con up CONNECTION_NAME
```

---

# VLANs

## Temporary VLAN

Create VLAN 20 on `ens18`:

```bash
sudo ip link add link ens18 name ens18.20 type vlan id 20
sudo ip addr add 192.168.20.50/24 dev ens18.20
sudo ip link set ens18.20 up
```

Delete:

```bash
sudo ip link delete ens18.20
```

---

## Debian VLAN

```ini
auto ens18
iface ens18 inet manual

auto ens18.20
iface ens18.20 inet static
    address 192.168.20.50/24
    gateway 192.168.20.1
```

Apply:

```bash
sudo ifreload -a
```

---

## Netplan VLAN

```yaml
network:
  version: 2
  ethernets:
    ens18: {}
  vlans:
    vlan20:
      id: 20
      link: ens18
      addresses:
        - 192.168.20.50/24
      routes:
        - to: default
          via: 192.168.20.1
      nameservers:
        addresses:
          - 1.1.1.1
```

Apply:

```bash
sudo netplan apply
```

---

## RHEL VLAN with nmcli

```bash
sudo nmcli con add type vlan con-name vlan20 ifname ens18.20 dev ens18 id 20 \
  ipv4.addresses 192.168.20.50/24 \
  ipv4.gateway 192.168.20.1 \
  ipv4.dns "1.1.1.1" \
  ipv4.method manual
```

Bring up:

```bash
sudo nmcli con up vlan20
```

---

# Bridges

Bridges are common on virtualization hosts.

Examples:

- Proxmox: `vmbr0`
- KVM/libvirt: `virbr0`
- Manual bridge: `br0`

## Show bridges

```bash
bridge link
```

or:

```bash
ip link show type bridge
```

---

## Debian bridge

Example bridge using `ens18` as the physical port:

```ini
auto lo
iface lo inet loopback

auto ens18
iface ens18 inet manual

auto vmbr0
iface vmbr0 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    bridge-ports ens18
    bridge-stp off
    bridge-fd 0
    dns-nameservers 1.1.1.1 8.8.8.8
```

Apply:

```bash
sudo ifreload -a
```

Important: when using a bridge, the IP normally belongs on the bridge, not the physical NIC.

---

## Netplan bridge

```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: false
  bridges:
    br0:
      interfaces:
        - ens18
      addresses:
        - 192.168.1.50/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 1.1.1.1
      parameters:
        stp: false
        forward-delay: 0
```

Apply:

```bash
sudo netplan apply
```

---

## RHEL bridge with nmcli

Create bridge:

```bash
sudo nmcli con add type bridge ifname br0 con-name br0 \
  ipv4.addresses 192.168.1.50/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "1.1.1.1 8.8.8.8" \
  ipv4.method manual
```

Add physical interface as bridge port:

```bash
sudo nmcli con add type ethernet ifname ens18 con-name br0-port master br0
```

Bring up:

```bash
sudo nmcli con up br0
sudo nmcli con up br0-port
```

Disable old direct connection if needed:

```bash
sudo nmcli con down "Wired connection 1"
```

---

# Bonds

Bonding combines multiple physical NICs.

Common modes:

```text
active-backup
802.3ad
balance-rr
balance-xor
broadcast
balance-tlb
balance-alb
```

For simple redundancy, use `active-backup`.

For LACP, use `802.3ad` and configure the switch side too.

---

## Debian bond

```ini
auto bond0
iface bond0 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    bond-slaves ens18 ens19
    bond-mode active-backup
    bond-miimon 100
    dns-nameservers 1.1.1.1 8.8.8.8
```

Apply:

```bash
sudo ifreload -a
```

---

## RHEL bond with nmcli

Create bond:

```bash
sudo nmcli con add type bond ifname bond0 con-name bond0 mode active-backup \
  ipv4.addresses 192.168.1.50/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "1.1.1.1 8.8.8.8" \
  ipv4.method manual
```

Add ports:

```bash
sudo nmcli con add type ethernet ifname ens18 con-name bond0-port1 master bond0
sudo nmcli con add type ethernet ifname ens19 con-name bond0-port2 master bond0
```

Bring up:

```bash
sudo nmcli con up bond0
sudo nmcli con up bond0-port1
sudo nmcli con up bond0-port2
```

Check bond status:

```bash
cat /proc/net/bonding/bond0
```

---

# Firewall Checks

## Debian / Ubuntu: UFW

```bash
sudo ufw status verbose
sudo ufw allow ssh
sudo ufw allow 443/tcp
sudo ufw reload
```

---

## Red Hat / RHEL: firewalld

```bash
sudo firewall-cmd --state
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --list-all
```

Allow HTTP:

```bash
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
```

Allow custom port:

```bash
sudo firewall-cmd --add-port=8443/tcp --permanent
sudo firewall-cmd --reload
```

---

# Packet Capture

## Install tcpdump

Debian / Ubuntu:

```bash
sudo apt install tcpdump
```

Red Hat / RHEL:

```bash
sudo dnf install tcpdump
```

## Capture all traffic on interface

```bash
sudo tcpdump -i ens18
```

## Capture without DNS resolution

```bash
sudo tcpdump -ni ens18
```

## Capture ICMP

```bash
sudo tcpdump -ni ens18 icmp
```

## Capture host traffic

```bash
sudo tcpdump -ni ens18 host 192.168.1.10
```

## Capture port traffic

```bash
sudo tcpdump -ni ens18 port 443
```

## Save to pcap

```bash
sudo tcpdump -ni ens18 -w capture.pcap
```

Open the `.pcap` in Wireshark.

---

# Connectivity Troubleshooting

## Ping gateway

```bash
ping -c 4 192.168.1.1
```

## Ping public IP

```bash
ping -c 4 1.1.1.1
```

## Test DNS

```bash
dig example.com
```

or:

```bash
getent hosts example.com
```

## Trace route

Debian / Ubuntu:

```bash
sudo apt install traceroute
traceroute 8.8.8.8
```

Red Hat / RHEL:

```bash
sudo dnf install traceroute
traceroute 8.8.8.8
```

## Test TCP port

Debian / Ubuntu:

```bash
sudo apt install netcat-openbsd
nc -vz example.com 443
```

Red Hat / RHEL:

```bash
sudo dnf install nmap-ncat
nc -vz example.com 443
```

## Test HTTP

```bash
curl -I https://example.com
```

---

# Common Issues and Resolution Steps

## `ifreload: command not found`

Cause: `ifreload` comes from `ifupdown2`.

Fix:

```bash
sudo apt update
sudo apt install ifupdown2
sudo ifreload -a
```

---

## Changed `/etc/network/interfaces` but nothing happened

Cause: the config was edited but not reloaded.

Fix:

```bash
sudo ifreload -a
```

If needed:

```bash
sudo systemctl restart networking
```

Warning: restarting networking over SSH can disconnect you.

---

## `RTNETLINK answers: File exists`

Cause: an IP address or route already exists.

Check:

```bash
ip addr show
ip route
```

Use `replace` instead of `add`:

```bash
sudo ip route replace default via 192.168.1.1
```

Or delete the old route first:

```bash
sudo ip route del default
sudo ip route add default via 192.168.1.1
```

---

## No internet but local network works

Common causes:

- Missing default gateway
- Wrong default gateway
- DNS problem
- Firewall problem
- Upstream router problem

Check default route:

```bash
ip route show default
```

Expected example:

```text
default via 192.168.1.1 dev ens18
```

Test public IP:

```bash
ping -c 4 1.1.1.1
```

If public IP works but domains fail, it is DNS.

---

## Can ping IP but not domain name

Cause: DNS is broken or missing.

Check:

```bash
cat /etc/resolv.conf
resolvectl status
dig example.com
```

Debian fix:

```ini
dns-nameservers 1.1.1.1 8.8.8.8
```

Apply:

```bash
sudo ifreload -a
```

Netplan fix:

```yaml
nameservers:
  addresses:
    - 1.1.1.1
    - 8.8.8.8
```

Apply:

```bash
sudo netplan apply
```

RHEL fix:

```bash
sudo nmcli con mod CONNECTION_NAME ipv4.dns "1.1.1.1 8.8.8.8"
sudo nmcli con up CONNECTION_NAME
```

---

## Interface has no IP address

Check link state:

```bash
ip link show dev ens18
```

Bring up temporarily:

```bash
sudo ip link set ens18 up
```

If using DHCP on Debian / Ubuntu:

```bash
sudo dhclient -v ens18
```

If using NetworkManager:

```bash
sudo nmcli dev connect ens18
```

Check logs:

```bash
journalctl -u NetworkManager
journalctl -u networking
```

---

## Interface name changed after reboot

Check names:

```bash
ip link show
```

Fix the relevant config to use the new name.

Debian:

```bash
sudo nano /etc/network/interfaces
sudo ifreload -a
```

Netplan:

```bash
sudo nano /etc/netplan/*.yaml
sudo netplan apply
```

RHEL:

```bash
nmcli dev status
nmcli con show
sudo nmcli con mod CONNECTION_NAME connection.interface-name NEW_INTERFACE
sudo nmcli con up CONNECTION_NAME
```

---

## NetworkManager says device is unmanaged

Check:

```bash
nmcli dev status
```

Example:

```text
ens18 ethernet unmanaged
```

Common causes:

- Interface is configured in `/etc/network/interfaces`
- NetworkManager is configured not to manage it
- Cloud-init owns the interface

Check config:

```bash
grep -R unmanaged /etc/NetworkManager/NetworkManager.conf /etc/NetworkManager/conf.d/
```

Debian/Ubuntu example fix:

```bash
sudo nano /etc/NetworkManager/NetworkManager.conf
```

Change:

```ini
[ifupdown]
managed=false
```

to:

```ini
[ifupdown]
managed=true
```

Restart:

```bash
sudo systemctl restart NetworkManager
```

---

## Netplan config fails

Validate:

```bash
sudo netplan generate
```

Test safely:

```bash
sudo netplan try
```

Apply:

```bash
sudo netplan apply
```

Common YAML problems:

- Wrong indentation
- Tabs instead of spaces
- Missing colon
- Wrong interface name
- Multiple default routes without route metrics

---

## Duplicate default routes

Check:

```bash
ip route show default
```

Example problem:

```text
default via 192.168.1.1 dev ens18
default via 10.0.0.1 dev ens19
```

Temporary fix:

```bash
sudo ip route del default via 10.0.0.1 dev ens19
```

NetworkManager route metrics:

```bash
sudo nmcli con mod CONNECTION_1 ipv4.route-metric 100
sudo nmcli con mod CONNECTION_2 ipv4.route-metric 200
sudo nmcli con up CONNECTION_1
sudo nmcli con up CONNECTION_2
```

Lower metric wins.

---

## SSH disconnects after network changes

Cause: interface restarted, IP changed, route changed, or firewall changed.

Use a rollback timer before remote changes.

Debian rollback example:

```bash
sudo cp /etc/network/interfaces /etc/network/interfaces.bak
sudo bash -c 'sleep 120 && cp /etc/network/interfaces.bak /etc/network/interfaces && ifreload -a' &
sudo ifreload -a
```

If everything works, cancel rollback:

```bash
sudo pkill -f "sleep 120"
```

Using `at`:

```bash
echo "cp /etc/network/interfaces.bak /etc/network/interfaces && ifreload -a" | sudo at now + 2 minutes
```

Cancel queued rollback:

```bash
atq
sudo atrm JOB_ID
```

Install `at`:

Debian / Ubuntu:

```bash
sudo apt install at
```

Red Hat / RHEL:

```bash
sudo dnf install at
sudo systemctl enable --now atd
```

---

## Bridge has no connectivity

Check:

```bash
ip addr show
bridge link
ip route
```

Common causes:

- IP is on physical NIC instead of bridge
- Physical NIC is not added as bridge port
- Bridge is down
- Wrong gateway
- STP or forward delay issue
- Switch VLAN mismatch

Correct Debian bridge pattern:

```ini
auto ens18
iface ens18 inet manual

auto vmbr0
iface vmbr0 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    bridge-ports ens18
    bridge-stp off
    bridge-fd 0
```

Incorrect when using a bridge:

```ini
auto ens18
iface ens18 inet static
    address 192.168.1.50/24
```

---

## VLAN interface cannot reach network

Check:

```bash
ip -d link show ens18.20
ip addr show ens18.20
ip route
```

Common causes:

- Wrong VLAN ID
- Switch port not trunking/tagging the VLAN
- Native/untagged VLAN mismatch
- Gateway does not exist on that VLAN
- Firewall blocking traffic

Look for tagged VLAN traffic:

```bash
sudo tcpdump -eni ens18 vlan
```

---

## `nmcli con up` says no suitable device found

Common causes:

- Connection is bound to the wrong interface
- Interface name changed
- Device is unmanaged
- NIC is down or missing

Check:

```bash
nmcli dev status
nmcli con show CONNECTION_NAME
```

Fix interface binding:

```bash
sudo nmcli con mod CONNECTION_NAME connection.interface-name ens18
sudo nmcli con up CONNECTION_NAME
```

Or clear binding:

```bash
sudo nmcli con mod CONNECTION_NAME connection.interface-name ""
sudo nmcli con up CONNECTION_NAME
```

---

## `Temporary failure in name resolution`

Cause: DNS resolver unavailable or not configured.

Check:

```bash
cat /etc/resolv.conf
resolvectl status
ping -c 4 1.1.1.1
dig example.com
```

Temporary fix:

```bash
sudo bash -c 'echo "nameserver 1.1.1.1" > /etc/resolv.conf'
```

Note: this may be overwritten by NetworkManager, systemd-resolved, DHCP, or Netplan.

---

## Firewall allows service but connection still fails

Confirm service is listening:

```bash
sudo ss -tulpn | grep ':PORT'
```

Check firewall.

Debian / Ubuntu:

```bash
sudo ufw status verbose
```

Red Hat / RHEL:

```bash
sudo firewall-cmd --list-all
```

Check route back to client:

```bash
ip route get CLIENT_IP
```

Capture packets:

```bash
sudo tcpdump -ni ens18 port PORT
```

If packets arrive but no reply leaves, check firewall and service binding.

If packets never arrive, check upstream firewall, routing, NAT, or switch path.

---

# Safe Remote Networking Checklist

Before changing networking over SSH:

1. Save a backup.
2. Start a rollback timer.
3. Apply the change.
4. Confirm SSH still works.
5. Confirm gateway, DNS, and route.
6. Cancel the rollback.

## Debian example

```bash
sudo cp /etc/network/interfaces /etc/network/interfaces.bak
sudo bash -c 'sleep 120 && cp /etc/network/interfaces.bak /etc/network/interfaces && ifreload -a' &
sudo ifreload -a
ip addr
ip route
ping -c 4 1.1.1.1
dig example.com
sudo pkill -f "sleep 120"
```

## Red Hat / RHEL example

```bash
nmcli con show
sudo nmcli con mod CONNECTION_NAME ipv4.addresses 192.168.1.50/24
sudo nmcli con mod CONNECTION_NAME ipv4.gateway 192.168.1.1
sudo nmcli con mod CONNECTION_NAME ipv4.dns "1.1.1.1 8.8.8.8"
sudo nmcli con mod CONNECTION_NAME ipv4.method manual
sudo nmcli con up CONNECTION_NAME
ip addr
ip route
ping -c 4 1.1.1.1
dig example.com
```

---

# Command Cheat Sheet

## General

```bash
ip addr
ip link
ip route
ip route get 8.8.8.8
ss -tulpn
hostnamectl
```

## Debian / Ubuntu

```bash
sudo nano /etc/network/interfaces
sudo ifreload -a
sudo ifquery --list
sudo ifquery --check ens18
sudo systemctl restart networking
```

## Ubuntu Netplan

```bash
ls /etc/netplan/
sudo netplan generate
sudo netplan try
sudo netplan apply
```

## Red Hat / RHEL

```bash
nmcli dev status
nmcli con show
nmcli con show --active
sudo nmcli con mod CONNECTION_NAME ipv4.addresses 192.168.1.50/24
sudo nmcli con mod CONNECTION_NAME ipv4.gateway 192.168.1.1
sudo nmcli con mod CONNECTION_NAME ipv4.dns "1.1.1.1 8.8.8.8"
sudo nmcli con mod CONNECTION_NAME ipv4.method manual
sudo nmcli con up CONNECTION_NAME
sudo systemctl restart NetworkManager
```

## DNS

```bash
cat /etc/resolv.conf
resolvectl status
dig example.com
dig @1.1.1.1 example.com
getent hosts example.com
```

## Troubleshooting

```bash
ping -c 4 192.168.1.1
ping -c 4 1.1.1.1
traceroute 8.8.8.8
nc -vz example.com 443
curl -I https://example.com
sudo tcpdump -ni ens18
```
