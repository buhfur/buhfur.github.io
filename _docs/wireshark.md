
# Wireshark filters 

## TCP 3-way handshake 

**Show entire handshake**

```bash
tcp.flags.syn == 1 || tcp.flags.ack == 1
```

**Capture first SYN packet**

```bash
tcp.flags.ack == 0
```

**Filter by TCP destination port**
```bash
tshark -Y "tcp.dstport == <port>"
```
**Filter by SYN+ACK flags**
```bash
tshark -Y "(tcp.flags & 0x12) == 0x12" 
```
