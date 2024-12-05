

# Network Manager 




## Enable wake on LAN for machine 


1. Navigate to /etc/NetworkManager/system-connections

2. Find the file which matches the network interface you are using 

3. Open the file in your editor of choice 

4. The connection file might look something like this 


> Note: To check if the network interface supports Wake on LAN , use the command : `ethtool <interface-name>` and look for the **"Supports Wake-on :"** line in the output 


## Wake on LAN notes 

* Wake on LAN allows you to power on a computer remotely. It does this by receiving "Magic packets" which are received  by the host. 


* Magic packets use UDP port 9 

* If you are attempting to trigger Wake on LAN from outside the LAN , you will need to forward UDP port 9 and enable broadcast forwarding. Broadcast forwarding is disabled in most routers by default for security reasons 

* IMPORTANT: If you allow port 9/udp on a host , you WILL NOT see the port open if you attempt a port scan using nmap. However this will send a packet to the host using port 9. What you need to do is run wireshark or tcpdump  on the interface and listen for port 9 using the command below 

```bash
sudo tcpdump -i <interface-name> udp port 9 -w wol.pcap 
```

