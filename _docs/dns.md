

# DNS cheatsheet 


## Records 

A -> maps FQDN to IPv4 address 

AAAA -> maps FQDN to IPv6 address 

SOA -> Start of Authority records stores info about domains and it's used to direct how a dns zone propagates to secondary name servers.  

NS -> Name Server  , specifies which servers are authoritive for a domain or sub-domain , should not be pointed to CNAME.  

PTR -> Pointer record, maps IP addresses to hostnames 

SRV -> records used for Instant messaging or Voip , directed to separate host and port location 

TXT -> Allows admins to add additional info such as site ownership and email validation 

CNAME -> Record that acts like an alias , pionts to another domain or a subdomain , never points to an IP address. Allows you to make changes to the IP a domain points to without affecting bookmarks  

