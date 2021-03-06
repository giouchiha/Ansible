Role Name
=========

Installs and configures Samba (Ability to create AD Domains, file servers)

Requirements
------------

Install all Ansible role requirements.
````
sudo ansible-galaxy install -r requirements.yml -f
````

Vagrant
-------
Spin up Environment under Vagrant to test.
````
vagrant up
````
Will spin up an Ubuntu server to function as an Active Directory domain controller as well as a Server2012 server to manage from.

Role Variables
--------------

````
---
# defaults file for ansible-samba
pri_domain_name: 'example.org'
samba_ad_info:
  ad_dc_hostname: '{{ ansible_hostname }}'
  ad_dns_domain_name: '{{ pri_domain_name }}'
  adminpass: 'P@55w0rd'
  backend_dns:
  dns_forwarder: 8.8.8.8
  domain_admin_pass:
  kerberos_realm: '{{ pri_domain_name }}'
  netbios_domain_name: 'EXAMPLE'
samba_allow_guests: 'yes'  #Allow users who've been granted usershare privileges to create public shares, not just authenticated ones
samba_create_domain_controller: false  #defines if you are building an Active Directory domain controller
samba_create_domain_users: false  #defines if users defined in samba_domain_users list should be created
samba_domain_users:
  - name: 'test01'
    password: 'P@55w0rd'
  - name: 'test02'
    password: 'P@55w0rd'
samba_groups:  #define samba groups to create
  - 'nogroup'
  - 'securedgroup'
  - 'testgroup'
samba_security: 'user'  #Defines samba security
samba_server_role: 'standalone server'  #defines server role...(standalone server or active directory domain controller)
samba_share_path: '/mnt/samba/shares'  #defines root folder for samba shares to be created
samba_shares:
  - name: 'allaccess'
    browsable: 'yes'
    folder_perms: '0755'
    group: 'nogroup'
    guest_ok: 'yes'
    owner: 'nobody'
    read_only: 'no'
    writable: 'yes'
  - name: 'public'
    browsable: 'yes'
    folder_perms: '0777'
    group: 'nogroup'
    guest_ok: 'yes'
    owner: 'nobody'
    read_only: 'no'
    writable: 'yes'
  - name: 'secured'
    browsable: 'yes'
    folder_perms: '0770'
    group: 'securedgroup'
    guest_ok: 'no'
    valid_users: '@securedgroup'
    writable: 'yes'
  - name: 'test'
    browsable: 'yes'
    folder_perms: '0770'
    group: 'testgroup'
    guest_ok: 'no'
    valid_users: '@testgroup'
    writable: 'yes'
samba_users:  #define users to create
  - name: 'vagrant'  #define user name to create
    groups:  #define groups to add user to...ensure samba_groups names exist.
      - 'nogroup'
      - 'securedgroup'
      - 'testgroup'
    smbpasswd: 'P@55w0rd'  #define samba user password
samba_workgroup: 'EXAMPLE'
````

Dependencies
------------

You can install dependencies as follows:
````
sudo ansible-galaxy install -r requirements.yml -f
````

Example Playbook
----------------

#### GitHub
````
---
- name: provisions samba
  hosts: all
  become: true
  vars:
    - enable_dhcp_services: false
    - samba_create_domain_controller: true
    - samba_server_role: 'active directory domain controller'  #defines server role...(standalone server or active directory domain controller)
  roles:
    - role: ansible-isc-dhcp
      when: enable_dhcp_services is defined and enable_dhcp_services
    - role: ansible-ntp
    - role: ansible-samba
  tasks:
````
#### Galaxy
````
---
- name: provisions samba
  hosts: all
  become: true
  vars:
    - enable_dhcp_services: false
    - samba_create_domain_controller: true
    - samba_server_role: 'active directory domain controller'  #defines server role...(standalone server or active directory domain controller)
  roles:
    - role: mrlesmithjr.isc-dhcp
      when: enable_dhcp_services is defined and enable_dhcp_services
    - role: mrlesmithjr.ntp
    - role: mrlesmithjr.samba
  tasks:
````

License
-------

BSD

Author Information
------------------

Larry Smith Jr.
- @mrlesmithjr
- http://everythingshouldbevirtual.com
- mrlesmithjr [at] gmail.com
