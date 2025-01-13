# Python3 Network programming snippets



## Hostnames

This snippet will most likely return the loopback of the device , to get the IP from a specific interface , use the third-party library 'netiface'

```python 
import socket 
hostname = "www.google.com" # Remote hostname 

print(socket.gethostbyname(hostname))
print(socket.gethostname())

```
