
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

