


# Desktop environments in linux 


## View Desktop environments installed 

- Debian :
    ```bash
    dpkg --get-selections | grep -E 'gnome|kde|xfce|lxde|cinnamon|mate'
    ```

- RHEL ( Redhat , Fedora ) : 
    ```bash
    rpm -qa | grep -E 'gnome|kde|xfce|lxde|cinnamon|mate'
    ```

- Arch based distros : 
    ```bash
    pacman -Q | grep -E 'gnome|kde|xfce|lxde|cinnamon|mate' 
    ```
