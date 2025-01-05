

# Syslog 


# Configuration 

* Configuration file is located in : /etc/rsyslog.conf


## Send logs to remote server 

Put this line in your /etc/rsyslog.conf file or where ever your config file is located 
    ```bash
    *.* @<REMOTE-SERVER-IP>:514 # UDP 
    *.* @@<REMOTE-SERVER-IP>:514 # TCP
    ```

Then restart the rsyslog service 


## Receive logs from Clients 

Enable reception in your config file 

```bash
# UDP syslog reception 

module(load="imudp")
input(type="imudp" port="514")

# TCP syslog reception 

module(load="imtcp")
input(type="imtcp" port="514")
```

Remember to add policy for the ports listed in your firewall configuration.

Verify the service is listening on port 514 

```bash
ss -tunlp | grep 514
```
