

# Proxmox Snippets 


## Enable LVM tools inside container 

First navigate to the containers config file in `/etc/pve/lxc/<container-id>`. Then add the line below in the config file.

```bash
lxc.mount.entry: /dev/mapper dev/mapper none bind,optional,create=dir
```

After this is done, install the *lvm2* package. 


