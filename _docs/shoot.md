# Notes for advanced troubleshooting on RHEL 


**Issues , how to configure, and what to configure**


POST -> Hardware configuration : 

- best fix is to replace hardware 



Selecting boot device : 

- bios / uefi menu -> replace hardware or use rescue system 

Loading the bootloader -> use grub2-install to configure and also edit the 
/etc/defaults/grub file to make changes. 

To troubleshoot use the GRUB boot prompt and edi /etc/defaults/grub followed by grub2-mkconfig 


Loading the Kernel : 

same as the last phase, make edits to /etc/default/grub and then grub2-mkconfig 


Starting /sbin/init : 

- This is compiled into initramfs so this can't be configured. 
- To troubleshoot , use the "init=" kernel boot argument or the rd.break kernel argument 


Processing initrd.target : 

- This is also compiled into initramfs , so can't be configured 
- To resolve issues with this phase , use the dracut command 


Switch to the root file : 

- to configure this , edit the /etc/fstab file ( I have this issue alot sometimes ) 
- To resolve it , like before edit the /etc/fstab file. 

Running the default target : 

- to configure this you can set the default target for systemd to boot into. AKA use the systemctl set-default command to boot into another target that can be isolated. To find out which targets can be isolated , navigate to the /usr/lib/systemd/system and run 

`grep Isolate *.target` 

This shows you which targets can be used as default as they can be Isolated.
