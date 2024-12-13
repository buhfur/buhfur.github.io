

# Misc Snippets 


## one liners 

### `ip` 
Get IP from interface in bash script using cut , awk , and ip

`TUN_IP=$(ip -o -4 addr show $INT | awk '{print $4}' | cut -d/ -f1)`




