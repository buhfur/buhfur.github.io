---
title: Proxmox
parent: Misc
grand_parent: Linux
nav_order: 130
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC
{:toc}

---



# Proxmox VE 

A practical copy/paste reference for common Proxmox VE administration tasks, troubleshooting, networking, storage, backups, and `pveproxy` issues.

> Replace example values like `VMID`, `CTID`, `NODE`, `vmbr0`, `local-lvm`, and IP addresses with values from your environment.

---


## Quick Status Checks

### Show Proxmox version

```bash
pveversion
pveversion -v
```

### Show node uptime and load

```bash
uptime
hostnamectl
```

### Show running Proxmox services

```bash
systemctl --type=service --state=running | grep -E 'pve|corosync|qemu|lxc'
```

### Show failed services

```bash
systemctl --failed
```

### Show storage status

```bash
pvesm status
```

### Show VM and container list

```bash
qm list
pct list
```

### Show all guests using Proxmox resource manager

```bash
pvesh get /cluster/resources --type vm
```

### Show node summary

```bash
pvesh get /nodes/$(hostname)/status
```

---

## Service Management

### Restart major Proxmox services

```bash
systemctl restart pvedaemon
systemctl restart pveproxy
systemctl restart pvestatd
systemctl restart pve-cluster
```

### Check service status

```bash
systemctl status pveproxy --no-pager
systemctl status pvedaemon --no-pager
systemctl status pvestatd --no-pager
systemctl status pve-cluster --no-pager
```

### Follow logs for a service

```bash
journalctl -u pveproxy -f
journalctl -u pvedaemon -f
journalctl -u pvestatd -f
```

### Restart all Proxmox API/web services

```bash
systemctl restart pvedaemon pveproxy pvestatd
```

Use this when the web UI is acting strange, API calls hang, storage status is stale, or the UI does not match the CLI.

---

## pveproxy Web UI Service

`pveproxy` is the Proxmox VE web proxy service. It listens on TCP port `8006` and serves the Proxmox web interface and API over HTTPS.

Default UI URL:

```text
https://PROXMOX-IP:8006
```

### Check if pveproxy is running

```bash
systemctl status pveproxy --no-pager
```

### Restart pveproxy

```bash
systemctl restart pveproxy
```

### Check if port 8006 is listening

```bash
ss -lntp | grep ':8006'
```

Expected example:

```text
LISTEN 0 4096 0.0.0.0:8006 0.0.0.0:* users:(("pveproxy",pid=1234,fd=6))
```

### Follow pveproxy logs

```bash
journalctl -u pveproxy -f
```

### Show recent pveproxy logs

```bash
journalctl -u pveproxy --since "30 minutes ago" --no-pager
```

### Check pveproxy process

```bash
ps aux | grep '[p]veproxy'
```

### Test the web UI locally from the node

```bash
curl -k https://127.0.0.1:8006
```

A successful response usually returns HTML or redirects, not a connection refused error.

### Test the API locally

```bash
curl -k https://127.0.0.1:8006/api2/json/version
```

### Common pveproxy issue: web UI will not load

Symptoms:

- Browser says connection refused
- `https://IP:8006` does not load
- `ss -lntp | grep ':8006'` shows nothing

Checks:

```bash
systemctl status pveproxy --no-pager
journalctl -u pveproxy --since "30 minutes ago" --no-pager
ss -lntp | grep ':8006'
```

Resolution:

```bash
systemctl restart pveproxy
systemctl restart pvedaemon
```

If it still fails:

```bash
systemctl restart pve-cluster
systemctl restart pvedaemon pveproxy pvestatd
```

### Common pveproxy issue: web UI loads but login fails

Symptoms:

- Login loops
- Permission denied with correct credentials
- Realm issues such as `pam` or `pve`
- API authentication errors

Checks:

```bash
journalctl -u pvedaemon --since "30 minutes ago" --no-pager
journalctl -u pveproxy --since "30 minutes ago" --no-pager
pveum user list
```

Verify the correct login format:

```text
root@pam
user@pve
user@pam
```

Restart auth/API services:

```bash
systemctl restart pvedaemon pveproxy
```

### Common pveproxy issue: certificate warning

The default Proxmox certificate is self-signed, so browsers usually warn about it.

Check certificate files:

```bash
ls -l /etc/pve/local/pve-ssl.key
ls -l /etc/pve/local/pve-ssl.pem
```

Renew Proxmox local certificates:

```bash
pvecm updatecerts --force
systemctl restart pveproxy
```

### Common pveproxy issue: bad or missing certificate files

Symptoms:

- `pveproxy` fails to start
- Logs mention SSL, certificate, or key errors
- Web UI fails after hostname or cluster changes

Check logs:

```bash
journalctl -u pveproxy --since "1 hour ago" --no-pager
```

Regenerate certificates:

```bash
pvecm updatecerts --force
systemctl restart pveproxy
```

If clustered, run certificate updates carefully and confirm cluster health first:

```bash
pvecm status
```

### Common pveproxy issue: hostname resolution broken

Proxmox expects the node hostname to resolve correctly. Bad `/etc/hosts` entries can break certificates, clustering, and web services.

Check hostname:

```bash
hostname
hostname -f
cat /etc/hostname
cat /etc/hosts
```

Example `/etc/hosts` entry:

```text
127.0.0.1 localhost.localdomain localhost
192.168.1.10 pve1.example.local pve1
```

After fixing hostname resolution:

```bash
pvecm updatecerts --force
systemctl restart pveproxy pvedaemon
```

### Common pveproxy issue: proxy or NAT access problems

Symptoms:

- Web UI works locally but not remotely
- Works from LAN but not WAN
- Browser times out instead of connection refused

Checks:

```bash
ip addr
ip route
ss -lntp | grep ':8006'
ping PROXMOX-IP
curl -k https://PROXMOX-IP:8006
```

Check firewalls between the client and Proxmox:

```bash
iptables -S
nft list ruleset
pve-firewall status
```

If accessing through a router, firewall, or reverse proxy, confirm TCP `8006` is allowed or forwarded to the Proxmox node.

### Common pveproxy issue: 500 error in web UI

Symptoms:

- UI opens but pages show `500`
- Storage or node sections fail to load
- VM console/API actions fail

Check backend services:

```bash
systemctl status pvedaemon --no-pager
systemctl status pvestatd --no-pager
journalctl -u pvedaemon --since "30 minutes ago" --no-pager
journalctl -u pvestatd --since "30 minutes ago" --no-pager
```

Restart backend services:

```bash
systemctl restart pvedaemon pvestatd pveproxy
```

### Common pveproxy issue: web UI slow or hangs

Possible causes:

- DNS lookup delays
- Broken storage mount
- Unreachable NFS/CIFS/iSCSI storage
- Cluster quorum problem
- High disk I/O wait

Checks:

```bash
pvesm status
pvecm status
df -h
iostat -xz 1 5
journalctl -u pvestatd --since "30 minutes ago" --no-pager
```

If an external storage is stale or unreachable, disable it temporarily from the CLI:

```bash
pvesm set STORAGE_ID --disable 1
systemctl restart pvestatd pvedaemon pveproxy
```

Re-enable it after fixing the storage path/network:

```bash
pvesm set STORAGE_ID --disable 0
```

### Common pveproxy issue: cannot open noVNC console

Symptoms:

- VM console opens blank
- noVNC disconnects
- Web UI works, console does not

Checks:

```bash
systemctl status pveproxy --no-pager
journalctl -u pveproxy --since "30 minutes ago" --no-pager
qm status VMID
```

Restart proxy/API services:

```bash
systemctl restart pveproxy pvedaemon
```

Also verify the browser or reverse proxy supports WebSockets. noVNC requires WebSocket traffic to pass correctly.

### Common pveproxy issue: ACME/Let's Encrypt certificate not applying

Check ACME account and certificate status from the UI or CLI:

```bash
pvenode acme account list
pvenode cert info
```

Restart the proxy after certificate changes:

```bash
systemctl restart pveproxy
```

### Common pveproxy issue: service starts then exits

Check detailed logs:

```bash
journalctl -xeu pveproxy --no-pager
```

Validate basic dependencies:

```bash
systemctl status pve-cluster --no-pager
systemctl status pvedaemon --no-pager
ls -l /etc/pve
```

If `/etc/pve` is unavailable or empty, the Proxmox cluster filesystem may not be mounted correctly.

Restart cluster filesystem:

```bash
systemctl restart pve-cluster
```

Then restart web services:

```bash
systemctl restart pvedaemon pveproxy pvestatd
```

---

## Cluster and Node Commands

### Show cluster status

```bash
pvecm status
```

### Show nodes

```bash
pvecm nodes
```

### Show cluster config

```bash
cat /etc/pve/corosync.conf
```

### Check quorum

```bash
pvecm status | grep -i quorum
```

### Expected single-node cluster quorum

For a standalone node, you may see only one node and quorum should still be present.

### Force expected votes to 1 during recovery

Use only when you understand the cluster state and need to recover quorum.

```bash
pvecm expected 1
```

### Restart cluster services

```bash
systemctl restart corosync
systemctl restart pve-cluster
```

### Show cluster filesystem mount

```bash
mount | grep /etc/pve
```

### Check if `/etc/pve` is accessible

```bash
ls -la /etc/pve
```

---

## VM Commands

### List VMs

```bash
qm list
```

### Show VM config

```bash
qm config VMID
```

### Start VM

```bash
qm start VMID
```

### Stop VM gracefully

```bash
qm shutdown VMID
```

### Force stop VM

```bash
qm stop VMID
```

### Reboot VM

```bash
qm reboot VMID
```

### Reset VM

```bash
qm reset VMID
```

### Show VM status

```bash
qm status VMID
```

### Enter VM monitor

```bash
qm monitor VMID
```

### Unlock locked VM

```bash
qm unlock VMID
```

### Clone VM

```bash
qm clone SOURCE_VMID NEW_VMID --name NEW_NAME
```

### Full clone to specific storage

```bash
qm clone SOURCE_VMID NEW_VMID --name NEW_NAME --full true --storage local-lvm
```

### Convert VM to template

```bash
qm template VMID
```

### Resize VM disk

```bash
qm resize VMID scsi0 +20G
```

After resizing the virtual disk, expand the partition/filesystem inside the guest OS.

### Add cloud-init drive

```bash
qm set VMID --ide2 local-lvm:cloudinit
```

### Set boot order

```bash
qm set VMID --boot order=scsi0
```

### Set CPU and memory

```bash
qm set VMID --cores 4 --memory 8192
```

### Add network adapter

```bash
qm set VMID --net0 virtio,bridge=vmbr0
```

### Add serial console for Linux VM

```bash
qm set VMID --serial0 socket --vga serial0
```

---

## LXC Container Commands

### List containers

```bash
pct list
```

### Show container config

```bash
pct config CTID
```

### Start container

```bash
pct start CTID
```

### Shutdown container

```bash
pct shutdown CTID
```

### Force stop container

```bash
pct stop CTID
```

### Enter container shell

```bash
pct enter CTID
```

### Run command inside container

```bash
pct exec CTID -- hostnamectl
```

### Unlock locked container

```bash
pct unlock CTID
```

### Resize container root disk

```bash
pct resize CTID rootfs +10G
```

### Create privileged container

```bash
pct create CTID local:vztmpl/debian-12-standard_VERSION_amd64.tar.zst \
  --hostname mycontainer \
  --storage local-lvm \
  --rootfs local-lvm:8 \
  --memory 1024 \
  --cores 2 \
  --net0 name=eth0,bridge=vmbr0,ip=dhcp
```

### Create unprivileged container

```bash
pct create CTID local:vztmpl/debian-12-standard_VERSION_amd64.tar.zst \
  --hostname mycontainer \
  --unprivileged 1 \
  --storage local-lvm \
  --rootfs local-lvm:8 \
  --memory 1024 \
  --cores 2 \
  --net0 name=eth0,bridge=vmbr0,ip=dhcp
```

### Enable nesting for Docker inside LXC

```bash
pct set CTID --features nesting=1
```

For Docker, a VM is usually cleaner and safer than LXC, but nesting can work for lab environments.

---

## Storage Commands

### Show storage status

```bash
pvesm status
```

### Show storage config

```bash
cat /etc/pve/storage.cfg
```

### List content on storage

```bash
pvesm list STORAGE_ID
```

Example:

```bash
pvesm list local
pvesm list local-lvm
```

### Scan disks

```bash
lsblk -f
blkid
fdisk -l
```

### Show disk health basics

```bash
smartctl -a /dev/sdX
```

Install if needed:

```bash
apt update
apt install smartmontools
```

### Add directory storage

```bash
mkdir -p /mnt/proxmox-backups
pvesm add dir backup-dir --path /mnt/proxmox-backups --content backup,iso,vztmpl
```

### Disable storage

```bash
pvesm set STORAGE_ID --disable 1
```

### Enable storage

```bash
pvesm set STORAGE_ID --disable 0
```

### Remove storage config only

```bash
pvesm remove STORAGE_ID
```

This removes the Proxmox storage definition. It does not necessarily delete the underlying data.

---

## LVM and Thin Pool Snippets

### Show volume groups

```bash
vgs
```

### Show logical volumes

```bash
lvs -a
```

### Show physical volumes

```bash
pvs
```

### Show thin pool usage

```bash
lvs -a -o+seg_monitor,data_percent,metadata_percent
```

### Extend thin pool with free VG space

```bash
lvextend -l +100%FREE /dev/pve/data
```

### Extend thin pool metadata

```bash
lvextend --poolmetadatasize +1G /dev/pve/data
```

### Repair thin pool metadata

Use with caution and only after backups or when recovering a broken pool.

```bash
lvconvert --repair /dev/pve/data
```

### Show disks used by LVM

```bash
pvs -o+pv_used
```

### Common issue: local-lvm is full

Symptoms:

- VMs pause or fail to start
- Backups fail
- `TASK ERROR: no space left on device`
- Thin pool `Data%` near `100`

Checks:

```bash
pvesm status
lvs -a -o+seg_monitor,data_percent,metadata_percent
```

Resolution options:

1. Delete unused VM disks from the Proxmox UI or CLI.
2. Move disks to another storage.
3. Extend the underlying VG/pool.
4. Remove old snapshots.

List unused disks in VM configs:

```bash
qm config VMID | grep unused
```

Remove unused disk from config:

```bash
qm set VMID --delete unused0
```

---

## Networking Snippets

### Show interfaces

```bash
ip addr
ip link
```

### Show routes

```bash
ip route
```

### Show bridge config

```bash
cat /etc/network/interfaces
```

### Restart networking

Use caution over SSH. This can disconnect you.

```bash
ifreload -a
```

### Example basic Linux bridge

```text
auto lo
iface lo inet loopback

iface eno1 inet manual

auto vmbr0
iface vmbr0 inet static
    address 192.168.1.10/24
    gateway 192.168.1.1
    bridge-ports eno1
    bridge-stp off
    bridge-fd 0
```

### Example bridge with VLAN awareness

```text
auto vmbr0
iface vmbr0 inet static
    address 192.168.1.10/24
    gateway 192.168.1.1
    bridge-ports eno1
    bridge-stp off
    bridge-fd 0
    bridge-vlan-aware yes
    bridge-vids 2-4094
```

### Assign VM NIC to VLAN tag

```bash
qm set VMID --net0 virtio,bridge=vmbr0,tag=20
```

### Assign LXC NIC to VLAN tag

```bash
pct set CTID --net0 name=eth0,bridge=vmbr0,ip=dhcp,tag=20
```

### Check bridge forwarding table

```bash
bridge fdb show
```

### Check VLANs on bridge

```bash
bridge vlan show
```

### Common issue: VM has no network

Checks:

```bash
qm config VMID | grep net
ip addr
cat /etc/network/interfaces
bridge link
```

Inside the VM, check:

```bash
ip addr
ip route
ping 1.1.1.1
ping google.com
```

Common fixes:

- Confirm VM NIC is attached to the correct bridge.
- Confirm the physical NIC is assigned to the bridge.
- Confirm VLAN tag is correct or removed if not needed.
- Confirm DHCP exists on that network.
- Confirm guest OS has the correct NIC driver.

---

## Firewall Snippets

### Check firewall status

```bash
pve-firewall status
```

### Start/stop firewall service

```bash
systemctl status pve-firewall --no-pager
systemctl restart pve-firewall
```

### Show generated firewall rules

```bash
iptables-save
nft list ruleset
```

### Datacenter firewall config

```bash
cat /etc/pve/firewall/cluster.fw
```

### Host firewall config

```bash
cat /etc/pve/nodes/$(hostname)/host.fw
```

### VM firewall config

```bash
cat /etc/pve/firewall/VMID.fw
```

### Allow Proxmox web UI port

In the Proxmox firewall UI or config, allow TCP `8006` from trusted management IPs.

Example rule concept:

```text
IN ACCEPT -source MANAGEMENT_IP -p tcp -dport 8006
```

---

## Backups and Restores

### Run backup of one VM

```bash
vzdump VMID --storage STORAGE_ID --mode snapshot --compress zstd
```

### Run backup of one container

```bash
vzdump CTID --storage STORAGE_ID --mode snapshot --compress zstd
```

### Backup all guests

```bash
vzdump --all --storage STORAGE_ID --mode snapshot --compress zstd
```

### List backups on storage

```bash
pvesm list STORAGE_ID --content backup
```

### Restore VM backup

```bash
qmrestore /path/to/backup.vma.zst NEW_VMID --storage local-lvm
```

### Restore LXC backup

```bash
pct restore NEW_CTID /path/to/backup.tar.zst --storage local-lvm
```

### Common issue: backup fails due to lock

Check lock:

```bash
qm config VMID | grep lock
pct config CTID | grep lock
```

Unlock:

```bash
qm unlock VMID
pct unlock CTID
```

Only unlock if you are sure no backup, restore, migration, or disk task is still running.

---

## Snapshots

### List VM snapshots

```bash
qm listsnapshot VMID
```

### Create VM snapshot

```bash
qm snapshot VMID SNAPSHOT_NAME --description "before changes"
```

### Roll back VM snapshot

```bash
qm rollback VMID SNAPSHOT_NAME
```

### Delete VM snapshot

```bash
qm delsnapshot VMID SNAPSHOT_NAME
```

### List LXC snapshots

```bash
pct listsnapshot CTID
```

### Create LXC snapshot

```bash
pct snapshot CTID SNAPSHOT_NAME --description "before changes"
```

### Roll back LXC snapshot

```bash
pct rollback CTID SNAPSHOT_NAME
```

### Delete LXC snapshot

```bash
pct delsnapshot CTID SNAPSHOT_NAME
```

---

## ISO and Template Management

### List ISO files

```bash
pvesm list local --content iso
```

### ISO path

```bash
/var/lib/vz/template/iso
```

### LXC template path

```bash
/var/lib/vz/template/cache
```

### Download LXC templates from UI

Go to:

```text
Node > local storage > CT Templates > Templates
```

### Refresh available appliance templates

```bash
pveam update
pveam available
```

### Download container template

```bash
pveam download local debian-12-standard_VERSION_amd64.tar.zst
```

---

## PCI Passthrough Basics

### Check IOMMU groups

```bash
find /sys/kernel/iommu_groups/ -type l
```

### Check PCI devices

```bash
lspci -nn
```

### Check kernel command line

```bash
cat /proc/cmdline
```

### Intel IOMMU kernel option

```text
intel_iommu=on iommu=pt
```

### AMD IOMMU kernel option

```text
amd_iommu=on iommu=pt
```

### Update GRUB

```bash
update-grub
reboot
```

### VFIO modules

```bash
cat > /etc/modules-load.d/vfio.conf <<'EOF2'
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
EOF2
```

### Update initramfs

```bash
update-initramfs -u -k all
reboot
```

---

## Logs and Troubleshooting

### Main system logs

```bash
journalctl -xe
journalctl -f
```

### Proxmox task logs

Task logs are visible in the web UI at the bottom task pane.

CLI task log path pattern:

```text
/var/log/pve/tasks/
```

Find recent task logs:

```bash
find /var/log/pve/tasks -type f -mtime -1 -print
```

### Authentication logs

```bash
journalctl -u pvedaemon --since "1 hour ago" --no-pager
```

### VM start failures

```bash
qm start VMID
journalctl --since "10 minutes ago" --no-pager
qm config VMID
```

### Container start failures

```bash
pct start CTID
journalctl --since "10 minutes ago" --no-pager
pct config CTID
```

### Check disk space

```bash
df -h
du -h -d1 /var/lib/vz 2>/dev/null | sort -h
```

### Check memory

```bash
free -h
```

### Check CPU and I/O

```bash
top
iostat -xz 1
```

Install iostat if needed:

```bash
apt install sysstat
```

---

## Common Fixes

### Fix stale UI data

```bash
systemctl restart pvestatd pvedaemon pveproxy
```

### Fix locked VM

```bash
qm unlock VMID
```

### Fix locked container

```bash
pct unlock CTID
```

### Fix noVNC console not opening

```bash
systemctl restart pveproxy pvedaemon
```

Then refresh browser cache or try another browser.

### Fix `/etc/pve` not responding

```bash
systemctl status pve-cluster --no-pager
systemctl restart pve-cluster
ls -la /etc/pve
```

### Fix hostname mismatch

Check:

```bash
hostname
hostname -f
cat /etc/hosts
cat /etc/hostname
```

Then regenerate certs:

```bash
pvecm updatecerts --force
systemctl restart pveproxy pvedaemon
```

### Fix apt enterprise repo error on non-subscription installs

Disable enterprise repo:

```bash
sed -i 's/^deb/# deb/' /etc/apt/sources.list.d/pve-enterprise.list
```

Add no-subscription repo. Example for Proxmox VE 8 on Debian Bookworm:

```bash
cat > /etc/apt/sources.list.d/pve-no-subscription.list <<'EOF2'
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
EOF2
```

Update:

```bash
apt update
```

### Fix package database issues

```bash
apt update
apt --fix-broken install
dpkg --configure -a
```

### Reboot safely

Before rebooting, check running guests:

```bash
qm list
pct list
```

Shutdown all guests gracefully:

```bash
for id in $(qm list | awk 'NR>1 {print $1}'); do qm shutdown "$id"; done
for id in $(pct list | awk 'NR>1 {print $1}'); do pct shutdown "$id"; done
```

Reboot node:

```bash
reboot
```

---

## Useful File Paths

### Proxmox config filesystem

```text
/etc/pve
```

### VM configs

```text
/etc/pve/qemu-server/VMID.conf
```

### LXC configs

```text
/etc/pve/lxc/CTID.conf
```

### Storage config

```text
/etc/pve/storage.cfg
```

### Network config

```text
/etc/network/interfaces
```

### ISO storage path for `local`

```text
/var/lib/vz/template/iso
```

### Container template path for `local`

```text
/var/lib/vz/template/cache
```

### Backup path for `local`

```text
/var/lib/vz/dump
```

### Proxmox certificate files

```text
/etc/pve/local/pve-ssl.key
/etc/pve/local/pve-ssl.pem
```

### Cluster config

```text
/etc/pve/corosync.conf
```

### Task logs

```text
/var/log/pve/tasks
```

---

## Emergency Checklist: Web UI Down

Run from SSH or local console:

```bash
hostname
ip addr
ip route
ss -lntp | grep ':8006'
systemctl status pveproxy --no-pager
systemctl status pvedaemon --no-pager
systemctl status pve-cluster --no-pager
journalctl -u pveproxy --since "30 minutes ago" --no-pager
journalctl -u pvedaemon --since "30 minutes ago" --no-pager
ls -la /etc/pve
pvesm status
```

Common recovery sequence:

```bash
systemctl restart pve-cluster
systemctl restart pvedaemon pveproxy pvestatd
pvecm updatecerts --force
systemctl restart pveproxy
```

Then test:

```bash
curl -k https://127.0.0.1:8006/api2/json/version
ss -lntp | grep ':8006'
```

---

## Emergency Checklist: VM Will Not Start

```bash
qm config VMID
qm status VMID
qm unlock VMID
pvesm status
lvs -a -o+data_percent,metadata_percent
journalctl --since "30 minutes ago" --no-pager
qm start VMID
```

Common causes:

- VM is locked by failed task
- Storage is unavailable
- Thin pool is full
- ISO path is missing
- PCI passthrough device is unavailable
- Memory allocation is too high
- Disk image is missing or corrupted

---

## Emergency Checklist: Storage Full

```bash
df -h
pvesm status
lvs -a -o+data_percent,metadata_percent
qm list
pct list
```

Find large local files:

```bash
du -h -d1 /var/lib/vz 2>/dev/null | sort -h
find /var/lib/vz/dump -type f -printf '%s %p\n' | sort -n | tail -20
```

Remove old backups only after confirming they are no longer needed:

```bash
rm /var/lib/vz/dump/vzdump-*.zst
```

Prefer deleting from the Proxmox UI or using known exact backup names instead of wildcards.

---

## Notes

- Avoid editing files under `/etc/pve` unless you know what the file controls.
- Make backups before changing VM disks, storage, cluster config, or network config.
- Be careful with network reloads over SSH.
- For single-node homelabs, most pveproxy and web UI problems are caused by service state, certificates, hostname resolution, storage hangs, or firewall/routing issues.
