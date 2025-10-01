---

layout: default
title: "Cisco IPSec tunneling"

---

# Table of Contents  
{: .no_toc }

1. TOC 
{:toc}

---



## **General Info**

```bash
# Command
sh crypto session detail

# Output example

Interface: GigabitEthernet0/0
Crypto map tag: VPN-MAP, local addr 198.51.100.1

  Peer 203.0.113.5 port 500
    Session ID: 123
    IKEv1 SA: local 198.51.100.1/500 remote 203.0.113.5/500 Active
    FVRF: Internet
    IVRF: Customer_A

``` 

* Shows detailed info about IPSec tunnels. 
* FVRF is the VRF used to reach the VPN peers public IP 
* IVRF is the VRF that holds the private networks being tunneled. If blank than you might be looking at a Single VRF environment where all internal traffic lives in the global routing table. ( AKA uses default configuration ) 
* using the global table is simpler and less headache ( according to the internet ) 
* The `IKEv1` header shows info about the protocol being used to setup the IPSec tunnel 

## **Troubleshooting**

### **Step 1. Verify basic connectivity**

* Ping / traceroute to peers public ip from router 
    * if this fails , routing or FVRF issue most likely  

### **Step 2. Check IKE (Phase 1) Status**

```bash
show crypto isakmp sa detail      (IKEv1)
show crypto ikev2 sa detail       (IKEv2)
```

* What you want to see: ACTIVE (IKEv1) or ESTABLISHED (IKEv2).

* Common failures:
    * MM_NO_STATE, MM_WAIT_MSG2, etc. → Mismatch in ISAKMP policies (encryption/hash/DH/auth).
    * Authentication failure → wrong pre-shared key or certificate issue.
    * No response → peer unreachable or blocked by firewall.

### **Step 3. Check IPSec (Phase 2) Status**

```bash
Router# show crypto ipsec sa

interface: GigabitEthernet0/0
    Crypto map tag: VPN-MAP, local addr 198.51.100.1

   protected vrf: (none)
   local  ident (addr/mask/prot/port): (10.10.10.0/255.255.255.0/0/0)
   remote ident (addr/mask/prot/port): (10.20.20.0/255.255.255.0/0/0)
   current_peer 203.0.113.5 port 500
     PERMIT, flags={origin_is_acl,}
    #pkts encaps: 152, #pkts encrypt: 152, #pkts digest: 152
    #pkts decaps: 145, #pkts decrypt: 145, #pkts verify: 145
    #pkts compressed: 0, #pkts decompressed: 0
    #pkts not compressed: 152, #pkts compr. failed: 0
    #pkts not decompressed: 145, #pkts decompress failed: 0
    #send errors 0, #recv errors 0
```

* What These Counters Mean

    * pkts encaps / encrypt / digest → Outbound traffic (your router is encrypting & sending into the tunnel).
    * pkts decaps / decrypt / verify → Inbound traffic (your router is receiving & decrypting from the tunnel).

* Think of it like this:

    * Encrypt = traffic leaving you into the tunnel.
    * Decrypt = traffic coming from the peer out of the tunnel.

* Why They Matter

    * If encrypt counters increase but decrypt does not → You’re sending traffic into the tunnel, but nothing is coming back.
    *  Usually a crypto ACL mismatch, routing issue, or the peer isn’t decrypting/returning.
    * If decrypt counters increase but encrypt does not → Peer is sending traffic, but you’re not sending anything back.
    * Usually your LAN isn’t generating traffic, or your crypto ACL doesn’t match what you think.
    * If neither increases → Tunnel may be “up” (SA exists) but no interesting traffic is flowing.

* What you want to see: Inbound/Outbound SAs with incrementing packet counters.

* Common failures:
    * IKE Phase 1 is up but no IPSec SA → mismatch in transform set / proposal.
    * SA created but only encrypt or decrypt counters increment → traffic selectors/ACLs mismatch (one side doesn’t “see” the traffic).
    * Packets dropped → MTU, fragmentation, or NAT issues.

### **Step 4. Look at Logs / Debugs**

* Enable logging for crypto:

    ```bash
    debug crypto isakmp
    debug crypto ipsec
    ```

(⚠️ careful on production routers — can be CPU heavy).

* Look for:
    * NO_PROPOSAL_CHOSEN → mismatch in Phase 2 policy.
    * INVALID_ID_INFORMATION → proxy IDs / crypto ACLs don’t match.
    * AUTHENTICATION FAILED → PSK or cert mismatch

### **Step 5. Verify Routing**

* Ensure interesting traffic (defined in crypto ACLs) is routed toward the tunnel.
* If IVRF is configured, confirm the inside networks are reachable in that VRF.
* A missing route = tunnel won’t be initiated.

### **Step 6. Lifetime & Rekey Issues**

* Check lifetime mismatch (Phase 1/2 timers). Cisco usually handles differences, but some vendors are strict.

* If the tunnel comes up but drops periodically, lifetimes or dead peer detection (DPD) may be misaligned.
