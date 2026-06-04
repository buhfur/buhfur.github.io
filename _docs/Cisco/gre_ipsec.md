---
title: GRE Over IPSEC Tunnel troubleshooting 
parent: Cisco
nav_order: 60
layout: default
---
# Troubleshooting GRE over IPsec Tunnel Down

## Overview

When troubleshooting a GRE over IPsec tunnel, work from the outside in:

1. Verify underlay connectivity.
2. Verify IKE (Phase 1).
3. Verify IPsec (Phase 2).
4. Verify GRE tunnel status.
5. Verify routing and tunnel reachability.
6. Verify NAT and crypto ACLs.
7. Generate traffic and watch counters.
8. Use debugs if necessary.

---

# 1. Verify Underlay Connectivity

Confirm the routers can reach each other's public IP addresses.

## Ping Peer Public IP

```bash
ping <peer-public-ip>
```

## Trace Route

```bash
traceroute <peer-public-ip>
```

## Verify Route to Peer

```bash
show ip route <peer-public-ip>
```

### Expected Result

The peer's public IP should be reachable and routed correctly.

### Common Problems

- Missing route
- ISP outage
- Firewall blocking traffic
- Incorrect peer IP

---

# 2. Verify IKE Phase 1

For IKEv1:

```bash
show crypto isakmp sa
```

## Healthy State

```text
QM_IDLE
```

## Common States

| State | Meaning |
|---------|---------|
| QM_IDLE | Phase 1 complete |
| MM_NO_STATE | No response from peer |
| MM_WAIT_MSG | Waiting on peer |
| MM_KEY_EXCH | Key exchange in progress |
| DELETE | SA being removed |

### MM_NO_STATE Usually Indicates

- Peer unreachable
- UDP/500 blocked
- NAT issue
- Incorrect peer IP
- ISAKMP policy mismatch

---

# 3. Verify IPsec Phase 2

```bash
show crypto ipsec sa
```

## Important Counters

```text
pkts encaps:
pkts decaps:
```

### Healthy Tunnel

Both counters increase.

Example:

```text
pkts encaps: 12345
pkts decaps: 12340
```

### Encaps Increasing, Decaps Zero

Example:

```text
pkts encaps: 500
pkts decaps: 0
```

Possible causes:

- Remote peer down
- Return routing issue
- Firewall blocking ESP
- Incorrect crypto ACL on remote side

### Both Counters Zero

Possible causes:

- No interesting traffic
- Crypto ACL mismatch
- Tunnel traffic not matching policy

---

# 4. Verify GRE Tunnel Status

```bash
show interface tunnel 0
```

## Healthy

```text
Tunnel0 is up, line protocol is up
```

## Tunnel Up / Protocol Down

```text
Tunnel0 is up, line protocol is down
```

Usually indicates:

- GRE packets not returning
- Peer tunnel interface down
- GRE blocked

## Tunnel Down / Protocol Down

```text
Tunnel0 is down, line protocol is down
```

Usually indicates:

- Tunnel destination unreachable
- Tunnel source interface issue
- Missing route to peer

---

# 5. Verify Tunnel Configuration

Display tunnel configuration:

```bash
show run interface tunnel0
```

Example:

```text
interface Tunnel0
 ip address 10.0.0.1 255.255.255.252
 tunnel source GigabitEthernet0/0
 tunnel destination 1.2.3.4
```

## Verify Tunnel Destination Reachability

```bash
ping <tunnel-destination-public-ip> source <outside-interface-ip>
```

Example:

```bash
ping 1.2.3.4 source 2.2.2.2
```

---

# 6. Verify GRE is Being Protected by IPsec

GRE uses protocol 47.

Example output:

```text
IPSEC FLOW: permit 47 host 192.168.0.110 host <peer-public-ip>
```

## Protocol Reference

| Protocol | Number |
|-----------|----------|
| ICMP | 1 |
| TCP | 6 |
| UDP | 17 |
| GRE | 47 |
| ESP | 50 |

---

# 7. Verify Crypto ACLs

Display access lists:

```bash
show access-lists
```

Example GRE ACL:

```text
permit gre host <local-public-ip> host <remote-public-ip>
```

Equivalent:

```text
permit 47 host <local-public-ip> host <remote-public-ip>
```

### Common Problems

- Wrong source IP
- Wrong destination IP
- ACL not mirrored on peer
- GRE not included

---

# 8. Verify Tunnel Reachability

Ping across the tunnel.

Example:

```bash
ping 10.0.0.2 source 10.0.0.1
```

If tunnel IPs cannot communicate:

- GRE not functioning
- IPsec not passing traffic
- Routing issue

---

# 9. Verify Dynamic Routing Protocols

## EIGRP

```bash
show ip eigrp neighbors
```

## OSPF

```bash
show ip ospf neighbor
```

## BGP

```bash
show ip bgp summary
```

### Healthy EIGRP Example

```text
Address         Interface
10.0.0.2        Tu0
```

If no neighbors exist:

- Tunnel not forwarding traffic
- Routing protocol blocked
- Authentication mismatch

---

# 10. Verify NAT Exemption

Display NAT configuration:

```bash
show run | include nat
```

Traffic destined for the VPN peer should not be NATed.

### Symptoms of NAT Issues

- MM_NO_STATE
- Phase 2 failures
- One-way traffic
- Encaps increasing but decaps not increasing

---

# 11. Generate Traffic and Watch Counters

Display counters:

```bash
show crypto ipsec sa
```

Generate traffic:

```bash
ping <remote-lan-ip>
```

Check counters again:

```bash
show crypto ipsec sa
```

### What to Look For

| Result | Meaning |
|----------|----------|
| Encaps increasing | Local encryption working |
| Decaps increasing | Remote traffic returning |
| Encaps only | Return traffic missing |
| Neither increasing | Traffic not matching crypto ACL |

---

# 12. Useful Debug Commands

## ISAKMP / IKE

```bash
debug crypto isakmp
```

## IPsec

```bash
debug crypto ipsec
```

## GRE

```bash
debug tunnel
```

## Disable Debugs

```bash
undebug all
```

or

```bash
u all
```

---

# Fast Triage Workflow

When a GRE over IPsec tunnel is reported down:

## Step 1

Check Phase 1:

```bash
show crypto isakmp sa
```

Healthy:

```text
QM_IDLE
```

---

## Step 2

Check Phase 2:

```bash
show crypto ipsec sa
```

Verify:

```text
encaps
decaps
```

---

## Step 3

Check Tunnel Status:

```bash
show interface tunnel <number>
```

### Interpret Results

| Status | Meaning |
|----------|----------|
| Up / Up | Tunnel healthy |
| Up / Down | GRE not returning |
| Down / Down | Underlay issue |

---

## Step 4

Verify Route to Peer

```bash
show ip route <peer-public-ip>
```

---

## Step 5

Verify Crypto ACL

```bash
show access-lists
```

Confirm GRE traffic matches:

```text
permit gre host <local-public-ip> host <remote-public-ip>
```

---

# Common Failure Scenarios

## MM_NO_STATE

Most likely:

- Peer unreachable
- Firewall blocking UDP/500
- Wrong peer IP

---

## QM_IDLE But No Traffic

Most likely:

- Crypto ACL mismatch
- Routing issue
- Tunnel traffic not matching policy

---

## Encaps Increasing, Decaps Zero

Most likely:

- Remote side down
- Return routing issue
- Firewall blocking ESP

---

## Tunnel Up / Down

Most likely:

- GRE packets not returning
- Peer tunnel interface down
- GRE blocked

---

## Tunnel Down / Down

Most likely:

- Tunnel destination unreachable
- Missing route
- Tunnel source interface down

---

# High-Value Show Commands

```bash
show crypto isakmp sa
show crypto ipsec sa
show interface tunnel <number>
show run interface tunnel <number>
show ip route <peer-public-ip>
show access-lists
show ip eigrp neighbors
show ip ospf neighbor
```

These commands alone will usually identify the failure domain within a few minutes.
