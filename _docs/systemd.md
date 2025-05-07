---

layout: default
title: "Systemd Info & Templates"

---

# Table of Contents 

{: .no_toc }

1. TOC 
{:toc}

---


# Systemd unit syntax 
---

```bash
[Unit]

Description=    # Description of unit 
After=          # Unit that should run before this unit runs 
Before=         # Unit that should run after this unit 
Requires=       # Required units that have to be started , else this unit will not run 
Wants=          # Units that are desired but if not started, the unit will continue to run regardless
Conflicts=      # Units that cannot run alongside this unit 

[Service]       # Can change according to type of unit , see Type for examples 

Type=           # simple,forking,oneshot,dbus,notify,idle
ExecStart=      # Absolute Path for commands and arguments used to start service
ExecStartPre=   # commands run before ExecStart
ExecStartPost=  # Commands run after ExecStart
ExecStop=       # Commands run to stop the srevice , if not present, process for unit will be killed
ExecStopPost=   # Commands run after ExecStop 
User=           # User privellege to run service as 


[Install]

Alias=          # list of alternative names for the unit 
RequiredBy=     # List of units required by the unit , aka units that have this one set to Requires in the Unit section   
WantedBy=       # List of units that name this unit in their Wants  
Also=           # Contains list of units that will be installed when the unit is started 
and uninstalled with the unit is removed 

---

# Mount unit directives 

[Mount]         # Mount units also contain Install and Unit stanza 
What=           # Absolute path of device to mount 
Where=          # Absolute path of mount point  
Type=           # Optional setting, filesystem type 
Options=        # Comma separated list of mounting options 


```

# Template Units
---

## Oneshot Unit

```bash
# /etc/systemd/system/echo@.service
[Unit]
Description=One‑shot echo of “Hello %i”
After=network.target

[Service]
Type=oneshot
# %i is the instance name, e.g. “foo” if you run echo@foo
ExecStart=/usr/bin/echo "Hello %i"
```

## Simple Unit 

```bash
# /etc/systemd/system/myapp@.service
[Unit]
Description=MyApp instance %i
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
# run as dedicated user/group
User=myapp
Group=myapp
# load a per-instance config file named /etc/myapp/<instance>.conf
ExecStart=/usr/bin/myapp --config /etc/myapp/%i.conf
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

## Forking Unit

```bash
# /etc/systemd/system/yourdaemon@.service
[Unit]
Description=YourDaemon instance %i
After=network.target
Wants=network-online.target

[Service]
Type=forking
# If you have an environment file per instance:
# EnvironmentFile=/etc/yourdaemon/%i.env
User=youruser
Group=yourgroup
# PID file must match what the daemon writes
PIDFile=/var/run/yourdaemon/%i.pid
# Pass the instance name into your daemon as needed (e.g. to select a config)
ExecStart=/usr/bin/yourdaemon --config /etc/yourdaemon/%i.conf
# How to reload (if supported)
ExecReload=/bin/kill -HUP $MAINPID
# How to stop gracefully
ExecStop=/bin/kill -TERM $MAINPID
# Restart automatically on failure
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

## Docker Unit

```bash
# /etc/systemd/system/container@.service
[Unit]
Description=Docker container %i
After=docker.service
Requires=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
# start/stop the container by name
ExecStart=/usr/bin/docker start %i
ExecStop=/usr/bin/docker stop %i

[Install]
WantedBy=multi-user.target
```

## Podman Unit 

> Note: You can always use "podman generate systemd -f " to generate a systemd unit for a podman container 

```bash
# /etc/systemd/system/podman-container@.service
[Unit]
Description=Podman container %i
After=network-online.target
Wants=network-online.target

[Service]
# restart the container if it exits non-zero
Restart=on-failure
RestartSec=10s

# Run as rootless if you drop this file into a rootful system; you can also adjust User= to a non‑root user
#User=someuser
#Group=somegroup

# Pull image if missing, then run in detached mode.
# Replace "myrepo/%i:latest" with your repo/pattern
ExecStartPre=/usr/bin/podman pull docker.io/myrepo/%i:latest
ExecStart=/usr/bin/podman run \
    --name %i \
    --rm \
    -d \
    -p 80:80 \
    docker.io/myrepo/%i:latest

# Stop the container gracefully, then remove it
ExecStop=/usr/bin/podman stop -t 10 %i
ExecStopPost=/usr/bin/podman rm %i

# Ensure the unit stays “active” after ExecStart (since the container runs in the background)
Type=notify
NotifyAccess=main

[Install]
WantedBy=multi-user.target
```


# Template Timers
---

Timer units by default will act as the timer for the unit that contains the same name. Otherwise you can add `Unit=<yourunit>.[service,mount,socket]` below the "Timer" section to specify a alternate unit. 

## Daily timer 

```bash
# /etc/systemd/system/daily-task.timer
[Unit]
Description=Run periodic-task.service once every day

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
```


## Weekly timer 

```bash
# /etc/systemd/system/weekly-task.timer
[Unit]
Description=Run periodic-task.service once every week

[Timer]
OnCalendar=weekly
Persistent=true

[Install]
WantedBy=timers.target
```

## Monthly timer 

```bash
# /etc/systemd/system/monthly-task.timer
[Unit]
Description=Run periodic-task.service once every month

[Timer]
OnCalendar=monthly
Persistent=true

[Install]
WantedBy=timers.target
```

## Specific Day Timer

```bash

```
## Combined Daily, Weekly, Monthly timers 

```bash
# /etc/systemd/system/periodic-task.timer
[Unit]
Description=Run periodic-task.service daily, weekly, and monthly

[Timer]
# once a day at 00:00
OnCalendar=daily
# once a week (Monday at 00:00)
OnCalendar=weekly
# once a month (1st of month at 00:00)
OnCalendar=monthly

# catch up missed runs after downtime
Persistent=true

[Install]
WantedBy=timers.target
```

## 5-Minute Timer 

```bash
# /etc/systemd/system/five-min-task.timer
[Unit]
Description=Run five‑min‑task.service every 5 minutes

[Timer]
# trigger 5 minutes after the last run finishes
OnUnitActiveSec=5min
# allow for catch‑up if missed (e.g. machine was off)
Persistent=true

[Install]
WantedBy=timers.target
```

## Hourly Timer 

```bash
# /etc/systemd/system/hourly-task.timer
[Unit]
Description=Run hourly-task.service every hour

[Timer]
# trigger at minute zero of every hour
OnCalendar=hourly
# if the machine was off when a run was due, catch up on next boot
Persistent=true

[Install]
WantedBy=timers.target
```

## Specific Hour/Minute Timer 

```bash
# /etc/systemd/system/specific-task.timer
[Unit]
Description=Run specific-task.service at specific minute/hour schedules

[Timer]
# Example 1: at minute 30 of every hour
OnCalendar=*:30:00

# Example 2: at 02:15 every day
OnCalendar=02:15:00

# catch up missed runs after downtime
Persistent=true

[Install]
WantedBy=timers.target
```

## Specific Day of Month Timer

```bash
# /etc/systemd/system/march15.timer
[Unit]
Description=Timer: run march15.service on March 15th each year

[Timer]
# Fires on March 15 at 00:00:00 local time
OnCalendar=Mar 15
# If the system was powered off on Mar 15, run immediately on next boot
Persistent=true

[Install]
WantedBy=timers.target
```

# Template Mounts 
---

## Ext4 mount 

```bash
# /etc/systemd/system/etc-mnt-data.mount
[Unit]
Description=Mount Data Partition
After=local-fs.target

[Mount]
What=/dev/sdb1
Where=/mnt/data
Type=ext4
Options=defaults,noatime

[Install]
WantedBy=multi-user.target
```

## CIFS/Samba Mount 

```bash
# /etc/systemd/system/mnt-share.mount
[Unit]
Description=Mount CIFS Share
After=network-online.target
Wants=network-online.target

[Mount]
What=//fileserver/shared
Where=/mnt/share
Type=cifs
Options=_netdev,credentials=/etc/samba/creds,iocharset=utf8,vers=3.0

[Install]
WantedBy=multi-user.target
```

## NFSv4 

```bash
# /etc/systemd/system/media-music.mount
[Unit]
Description=Mount NFS Music Share
After=network-online.target
Wants=network-online.target

[Mount]
What=nas.example.com:/export/music
Where=/media/music
Type=nfs4
Options=_netdev,defaults

[Install]
WantedBy=multi-user.target
```

## NFSv4 Automount 

```bash
# /etc/systemd/system/media-music.automount
[Unit]
Description=Automount NFS Music Share

[Automount]
Where=/media/music
TimeoutIdleSec=60

[Install]
WantedBy=multi-user.target
```

## Btrfs Mount 

```bash
# /etc/systemd/system/srv-btrfs.mount
[Unit]
Description=Mount Btrfs on /srv/btrfs
After=local-fs.target

[Mount]
What=/dev/sdc1
Where=/srv/btrfs
Type=btrfs
# common options: subvol (or subvolid), compression, space_cache
Options=subvol=@data,compress=zstd,space_cache=v2,defaults

[Install]
WantedBy=multi-user.target
```

## Xfs Mount Unit 

```bash
# /etc/systemd/system/srv-xfs.mount
[Unit]
Description=Mount XFS on /srv/xfs
After=local-fs.target

[Mount]
What=/dev/sdd1
Where=/srv/xfs
Type=xfs
Options=defaults,noatime,discard

[Install]
WantedBy=multi-user.target
```

# Template Sockets 
---

## TCP Socket Unit 

```bash
# /etc/systemd/system/my-tcp@.socket
[Unit]
Description=TCP Socket for MyApp instance %i

[Socket]
# %i expands to the instance name (e.g. "8080")
ListenStream=%i
# set to "yes" to spawn a service per connection
Accept=no
# optional: drop privileges
SocketUser=myapp
SocketGroup=myapp
SocketMode=0660

[Install]
WantedBy=sockets.target
```

## UDP Socket Unit 

```bash
# /etc/systemd/system/my-udp@.socket
[Unit]
Description=UDP Socket for MyApp instance %i

[Socket]
ListenDatagram=%i
# UDP is always datagram-based; no per-connection fork needed
Accept=no
SocketUser=myapp
SocketGroup=myapp
SocketMode=0660

[Install]
WantedBy=sockets.target
```

## Unix Socket Mount 

```bash
# /etc/systemd/system/my-unix@.socket
[Unit]
Description=UNIX Domain Socket %i for MyApp

[Socket]
# create a socket at /run/myapp-%i.sock
ListenStream=/run/myapp-%i.sock
# ensure only owner may connect
SocketMode=0660
SocketUser=myapp
SocketGroup=myapp
Accept=no

[Install]
WantedBy=sockets.target
```

# Systemd Info/Notes
---

## Systemd init phase 

* takes advantage of UUID's and TPM's 
* default units are located in `/usr/lib/systemd/system`
* custom units are in `/etc/systemd/system`
* Units in `/etc/systemd/system` override units in `/usr/lib/systemd/system`
* `list-units --type=<unit-type>` to display units by their type 
* `systemctl list-dependencies <unit_name>` : Lists units the `unit_name` depends on 
* `systemctl list-dependencies --reverse <unit_name>` : lists units that depend on `unit_name`
* `systemctl --failed`: lists all failed units
* `systemctl --failed --type=<unit_type>`: lists all failed units of a specific type 


## Systemd unit types 

* service 
* socket
* busname
* target
* snapshot
* device
* mount
* automount
* swap
* timer
* path
* slice
* scope

## Systemd Unit status definitions 

* loaded: Loaded into memory 
* inactive (dead): unit not running 
* active (running): running with one or more active processes 
* active (exited): Completed configuration 
* active (waiting): Running and listening for request
* enabled: Will start when system boots 
* disabled: Will not start when system boots 
* static: Must be started by another service 

## Systemd Controlling Services 

* `systemctl enable <unit>`: Turns on service when system is booted
* `systemctl disable <unit>`: Does not turn on service at boot 
* `systemctl start <unit>`: Starts service immediately 
* `systemctl stop <unit>`: Stops service immediately 
* `systemctl restart <unit>`: Stops and starts unit 
* `systemctl reload <unit>`: Re-reads configuration and continues running 
* `systemctl mask <unit>`: Makes a unit available by creating a symbolic link to `/dev/null`
* `systemctl unmask <unit>`: Removes mask 

## Systemd runlevels 

* (0) runlevel0.target, poweroff.target
* (1) runlevel1.target, rescue.target
* (2) runlevel2.target, multi-user.target
* (3) runlevel3.target, multi-user.target
* (4) runlevel4.target, multi-user.target ( not used in SystemV )
* (5) runlevel5.target, graphical.target
* (6) runlevel6.target, reboot.target

## Systemd targets

* Targets are a group of units and other targets  
* These targets provide a specific function 
* `systemctl --type=target`: Lists all activated targets 
* `systemctl --type=target --all`: Lists all available targets 
* `default.target`: Link to default runlevel target file
* `systemctl get-default`: Prints the default runlevel target file 
* `systemctl set-default`: Command that sets the default runlevel target file referenced by `default.target`
* `systemctl isolate <runlevel-target-file>`: Changes runlevel , used to stop anything that is not apart of the target runlevel 

## Systemd boot errors 

* "Can't find hard drive" -> Hard drive is missing or BIOS/UEFI is misconfigured 
* "Can't find bootloader" -> GRUB2 is incorrectly configured, or misconfigured 
* "Can't load kernel" -> GRUB2 misconfiguration or kernel misconfiguration
* "Web service failed to start" -> Boot successful , some applications may be misconfigured 



