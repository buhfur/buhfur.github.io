
# Ansible notes 

## Ansible configuration 

/etc/ansible/hosts -> inventory file 
/etc/ansible/ansible.cfg -> default config 


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
