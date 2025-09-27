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
show crypto ipsec sa
```
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
