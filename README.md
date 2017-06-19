Taskserver [![Build Status](https://travis-ci.org/wilmardo/ansible-taskserver.svg?branch=master)](https://travis-ci.org/wilmardo/ansible-taskserver)
=========

Ansible role to setup a taskserver for Taskwarrior

Requirements
------------
None

Role Variables
--------------
There are several role variables, they can be set in the hosts_vars/group_vars:

### Variables for user management
Variables for organization and user creation.

| Variable name             | Default value         | Description         |
| ------------------------- | --------------------- | ------------------- |
| taskd_organizations       | undefined             | Organizations to be created
| taskd_users               | undefined             | The users to be created, define in a list with dicts. Organization must be in the taskd_organizations variable!

### Variables for Taskd
Setup variables for taskserver

| Variable name             | Default value         | Description         |
| ------------------------- | --------------------- | ------------------- |
| taskd_user                | taskd                 | The user taskd runs as
| taskd_group               | taskd                 | The group taskd runs as
| taskd_port                | 53589                 | The port to use for the taskserver (for any port under 1024 the taskserver needs to be run as root, NOT recommended)
| taskd_download_location   | /root                 | Location to download the tarball to, remote user needs write permission for this location
| taskd_install_location    | /opt/taskd/           | Taskd install location
| taskd_data_location:      | /var/taskd            | Taskd datadir (recommended is to NOT put it in your Taskd exec dir)
| taskd_user_cert_location: | /var/taskd/user_certs | Folder to save generated user certs to

### Variables for SSL locations
All the defaults are set for the selfsigned SSL certificates which the script will generate, so if you want to use selfsigned certificates you can leave these as they are! 
Selfsigned certificates are not recommended for production use but might be acceptable for personal use.
The following variables can be used to specify the locations of your certificates. 

| Variable name             | Default value                             | Description         |
| ------------------------- | ----------------------------------------- | ------------------- |
| taskd_client_cert         | {{ taskd_data_location }}/client.cert.pem | To install Plexupdate. Enable when not Ubuntu or Centos/Fedora 
| taskd_client_key          | {{ taskd_data_location }}/client.key.pem  | Plexupdate install location
| taskd_server_cert         | {{ taskd_data_location }}/server.cert.pem | Plexupdate config location
| taskd_server_key:         | {{ taskd_data_location }}/server.cert.pem | Set the cron wrapper for Plexupdate
| taskd_server_crl          | {{ taskd_data_location }}/server.crl.pem  | Enables autoupdates for Plex, will defer the update when Plex is in use
| taskd_ca_cert             | {{ taskd_data_location }}/ca.cert.pem     | Enables autoupdater for Plexupdate

### Variables for selfsigning
If you decide to go the selfsigning route, leave the above SSL location variables default and change the following to your needs. 
These will be used to create the selfsigned certificates.

| Variable name                    | Default value          | Description         |
| -------------------------------- | -----------------------| ------------------- |
| taskd_selfsigned                 | false                  | Set to true to enable the generation of selfsigned certificates
| taskd_selfsigned_cn              | undefined              | Common Name for your certificate (example: ack.tasktools.org)
| taskd_selfsigned_bits            | 4096                   | Bits for SSL certificate (2048 or 4096)
| taskd_selfsigned_expiration_days | 365                    | Days the certificate is valid for
| taskd_selfsigned_organization:   | Göteborg Bit Factory   | Certificate organization
| taskd_selfsigned_country         | SE                     | Certificate counrty
| taskd_selfsigned_state           | Västra Götaland        | Certificate state
| taskd_selfsigned_locality        | Göteborg               | Certificate locality

Dependencies
------------
None

Example Playbooks
----------------

### Example Playbook with selfsigned SSL
The following playbook uses selfsigned ssl certificates which might not be the best idea for production use but it might be acceptable for personal use.
Please change the your.domain.tld to a valid value. This is the bare minimum to get started, the generated certificate will have the Swedish default localization. It is better to define most of the selfsigning variables for your locality.

    - hosts: taskservers    
      roles:
         - { role: wilmardo.taskserver, taskd_selfsigned: true,  
                                        taskd_selfsigned_cn: your.domain.tld, 
                                        taskd_organizations: 'Public',
                                        taskd_users: [{name: 'First Last', organization: 'Public'}, {name: 'First Later', organization: 'Public'}] } 

### Example Playbook with authorized SSL
The following playbook gives an example when using SSL certificates provided by a authority. The paths are an example please change to the location of your SSL certificates.

    - hosts: taskservers    
      roles:
         - { role: wilmardo.taskserver, taskd_client_cert: /home/taskd/ssl/client.cert.pem, 
                                        taskd_client_key:/home/taskd/ssl/client.key.pem, 
                                        taskd_server_cert:/home/taskd/ssl/server.cert.pem, 
                                        taskd_server_key:/home/taskd/ssl/server.key.pem,  
                                        taskd_server_crl:/home/taskd/ssl/server.crl.pem, 
                                        taskd_ca_cert:/home/taskd/ssl/ca.cert.pem,
                                        taskd_organizations: 'Public',
                                        taskd_users: [{name: 'First Last', organization: 'Public'}, {name: 'First Later', organization: 'Public'}] } 

License
-------

BSD

Author Information
------------------

Wilmar den Ouden

https://wilmardenouden.nl