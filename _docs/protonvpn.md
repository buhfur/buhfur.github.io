
# Kill switch configuration 

The protonvpn killswitch exists to secure your connection in the event of an unexpected connection drop. 

There are two modes for the killswitch : 

- Block access on local LAN , best used for public wifi 
- Allow access on local LAN , used for home networks  

Since my hypervisor is bridging the network interface for the virtual machines, I don't think option 1 would be a problem



# Test out killswitch functionality 

[link to reddit post](https://www.reddit.com/r/ProtonVPN/comments/eljquu/how_to_activate_protonvpncli_app_builtin/)

I found a very helpful reddit thread about adding the killswitch functionality to the CLI 


In this thread someone posted about how to trigger the killswitch functionality for testing reasons.

To trigger the killswitch , just kill the OpenVPN process being used by the VPN.


`sudo pkill openvpn`

