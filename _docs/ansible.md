---

layout: default
title: "Ansible Notes"

---

# Ansible notes 

## Ansible configuration & Important locations 

/etc/ansible/hosts -> inventory file 
/etc/ansible/ansible.cfg -> default config 

/usr/lib/python3.11/dist-packages/ansible/modules -> Location of all base ansible modules. 

## hosts , Inventory file 

```bash
[groupname]
<ip-or-hostname> 
<ip-or-hostname> 
<ip-or-hostname> 

[groupname]
ansible_user=<username>
ansible_password=<password> 

```

## Snippets 

`ansible <group> -m <module>` -> syntax for modules in ansible 

`ansible linux -m ping` -> ping all hosts in group 

**Update all containers with sudo apt update**

`ansible <groupname> -m shell -a "apt update -y" --become `
