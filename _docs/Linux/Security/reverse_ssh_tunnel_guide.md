---
title: Reverse SSH tunnel guide 
parent: Utils
grand_parent: Security 
nav_order: 10
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# Reverse SSH Tunnel with X11 Forwarding

## Overview

This setup allows you to SSH into a Linux PC behind NAT/firewalls by
having the home PC initiate a reverse SSH tunnel to a publicly
accessible VPS.

``` text
Home PC (NAT) ---> VPS/Public Server <--- Work PC
      Reverse Tunnel              SSH/X11
```

------------------------------------------------------------------------

## Prerequisites

-   Home PC running an SSH server.
-   Publicly reachable VPS with OpenSSH server installed.
-   `AllowTcpForwarding yes` enabled on the VPS.
-   `GatewayPorts yes` if you want the tunnel accessible from outside
    the VPS (optional).
-   `X11Forwarding yes` on the VPS if you plan to use X11 forwarding.

------------------------------------------------------------------------

## 1. Create the reverse tunnel (run on the Home PC)

If your home SSH server listens on the default port (22):

``` bash
ssh -N -R 2222:localhost:22 vpsuser@your-vps
```

If your home SSH server uses a custom port (example: 5193):

``` bash
ssh -N -R 2222:localhost:5193 vpsuser@your-vps
```

### Notes

-   `vpsuser` is **your username on the VPS**, not your home PC
    username.
-   When prompted, enter the **VPS account password**.

------------------------------------------------------------------------

## 2. Verify the tunnel (run on the VPS)

``` bash
ssh -p 2222 homeuser@localhost
```

### Notes

-   `homeuser` is **your Linux username on the home PC**.
-   When prompted, enter the **home PC account password**.

If this works, the reverse tunnel is functioning correctly.

------------------------------------------------------------------------

## 3. Connect with X11 forwarding

From your work machine:

``` bash
ssh -X -L 3333:localhost:2222 vpsuser@your-vps
```

Open a second terminal on the work machine:

``` bash
ssh -X -p 3333 homeuser@localhost
```

Alternatively, configure `ProxyJump` in `~/.ssh/config` if preferred.

------------------------------------------------------------------------

## Common Caveats

### Home SSH server uses a non-default port

The reverse tunnel destination **must** match the SSH server port on the
home PC.

Wrong:

``` bash
-R 2222:localhost:22
```

Correct (SSH server on 5193):

``` bash
-R 2222:localhost:5193
```

### Wrong username

When creating the tunnel:

-   Use the **VPS username**.

When connecting through the tunnel:

-   Use the **home PC username**.

Example:

``` bash
# Home PC -> VPS
ssh -N -R 2222:localhost:5193 vpsuser@your-vps

# VPS -> Home PC
ssh -p 2222 homeuser@localhost
```

### Tunnel testing

On the VPS:

``` bash
ss -lnt | grep 2222
```

You should see port `2222` listening while the reverse tunnel is active.

------------------------------------------------------------------------

## Recommended

For a persistent tunnel, use `autossh` in a `systemd` service so the
connection automatically reconnects if interrupted.
