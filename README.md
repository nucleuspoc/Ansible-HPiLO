# Ansible-HPiLO
This project is essentially just to show how the modules are used. I will cover:
1) Install modules - Python, Ansible, HPiLO
2) Show how to use the hpilo_facts module
3) Show how to use the hpilo_boot module

## Ansible
```json
$ sudo apt-get install libssl-dev
$ sudo pip install ansible
```
To check the module has successfully installed and/or just want to see the version number (note, if no version number then module is not installed)
```json
$ pip list
```
We are looking for "ansible(version #)"

## Python and Pip
```json
$ sudo apt-get install python python-pip
```
To check the two module versions and if it has been successfully installed (note, if no version number then module is not installed)
```json
$ python --version
$ pip --version
```

## Install HPiLO Python module
```json
$ sudo add-apt-repository ppa:dennis/python
$ sudo apt-get update
$ sudo apt-get install python-hpilo
```
To check the module has successfully installed and/or just want to see the version number (note, if no version number then module is not installed)
To check version:
```json
$ pip list
```
We are looking for "python-hpilo(version #)"

Before going any further, I received help from the Ansible Module website:
- http://docs.ansible.com/ansible/latest/hpilo_facts_module.html
- http://docs.ansible.com/ansible/latest/hpilo_boot_module.html

The 3 playbooks on this page do not have alot of functionality however are key! If you are going to want to run the below playbooks, then its these high level YAML files that are required to be executed:
- [HPiLo_boot.yml](../master/HPiLo_boot.yml): This is to execute the hpilo_boot tasks in 'roles'
- [HPiLo_facts.yml](../master/HPiLo_facts.yml): This is to execute the hpilo_facts tasks in 'roles'
- [site.yml](../master/site.yml): Will play everything!
These 3 are key as they specify the hosts for each 'role'

## hpilo_facts
### Task playbook
[Ansible YAML file](../master/roles/hpilo_facts/tasks/main.yml)
```yaml
---
- hpilo_facts:
    host: "{{ iLo_IP }}"
    login: "{{ iLo_username }}"
    password: "{{ iLo_password }}"
  delegate_to: localhost
## Not all return values are required, add / remove what is required      
- debug: var=hw_bios_version # Output BIOS version
- debug: var=hw_product_name # Output Product Name
- debug: var=hw_bios_date # Output BIOS Date
- debug: var=hw_ethX # Output Interface Information, for each Interface
- debug: var=hw_system_serial # Output System Serial number
- debug: var=hw_eth_ilo # Output Interface Information for the ILO Network Interface
- debug: var=hw_uuid # Output Hardware UUID
- debug: var=hw_product_uuid # Output Product UUID
```
### Variable playbook
This will need editing based on your iLO information
[Ansible YAML file](../master/roles/hpilo_facts/vars/main.yml)
```yaml
---
iLo_IP: '0.0.0.0'
iLo_username: 'USERNAME'
iLo_password: 'PASSWORD'
```

## hpilo_boot
### Task playbook
[Ansible YAML file](../master/roles/hpilo_boot/tasks/main.yml)
```yaml
---    
- name: Boot the system with an ISO
  hpilo_boot:
    host: "{{ iLo_IP }}"
    login: "{{ iLo_username }}"
    password: "{{ iLo_password }}"
    media: cdrom
    image: "{{ iso_image }}"
  delegate_to: localhost

- name: Power off the server
  hpilo_boot:
    host: "{{ iLo_IP }}"
    login: "{{ iLo_username }}"
    password: "{{ iLo_password }}"
    state: poweroff
  delegate_to: localhost
```
### Variable playbook
This will need editing based on your iLO information
[Ansible YAML file](../master/roles/hpilo_boot/vars/main.yml)
```yaml
---
iLo_IP: '0.0.0.0'
iLo_username: 'USERNAME'
iLo_password: 'PASSWORD'
iso_image: 'ISO_URL'
```
