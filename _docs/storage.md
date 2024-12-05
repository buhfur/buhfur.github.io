

# Network Storage 

Goals : Decide whether I should buy a RAID enclosure or a NAS for my network storage while learning about the various technologies used which provide context and factor into my decision. 


# RAID 

redundant array of Independent disks 


* needs to have JBOD mode to allow the proxmox truenas vm to manage each individual disk and use redundancy and checksumming on the vm's. Otherwise the disks won't be able to be managed by zfs.   

# ZFS 
