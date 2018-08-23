# wilmardo.taskserver

[![Ansible Role](https://img.shields.io/ansible/role/21350.svg?style=flat-square)](https://galaxy.ansible.com/wilmardo/taskserver)
[![Travis (.org)](https://img.shields.io/travis/wilmardo/ansible-role-taskserver.svg?style=flat-square)](https://travis-ci.org/wilmardo/ansible-role-taskserver)
[![Ansible Role](https://img.shields.io/ansible/role/d/21350.svg?style=flat-square)](https://galaxy.ansible.com/wilmardo/taskserver)

Ansible role to setup a Taskserver(taskd) for Taskwarrior

## Requirements

None

## Role Variables

### Default usage
For default usage of this role you only need to define the following, for more advanced usage look at the [Advanced usage](#advanced-usage) section:

```yaml  
# Organizations to be created
taskd_organizations:
  - Public
# The users to be created, organization must be in the taskd_organizations variable!
taskd_users:
  - name: First Last
    organization: Public
```

### Advanced usage

For more advanced usage the following variables are available:
```yaml
# Taskd version to install, gets passed to git module
taskd_version: v1.1.0

# User taskd daemon runs as
taskd_user: taskd
# Group taskd daemon runs as
taskd_group: taskd
# The ip address for the taskserver, defaults to ansible default
taskd_ip_address: "{{ ansible_default_ipv4['address'] }}"
# The port to use for the taskserver (for any port under 1024 the taskserver needs to be run as root, NOT recommended)
taskd_port: 53589
# Download location for taskd tarball
taskd_download_location: "{{ ansible_env.HOME }}/taskd"
# Location to install taskd to
taskd_install_location: /opt/taskd
# Location for taskd data, sets the $TASKDDATA variable (recommended is to NOT put it in your Taskd exec dir)
taskd_data_location: /var/taskd
# Location to safe user.conf file to
taskd_user_conf_location: "{{ taskd_data_location }}/users"
# Location to safe user certificates to
taskd_user_cert_location: "{{ taskd_data_location }}/users/certs"

# Variables for ssl certs (defaults for selfsigned). Can be used to use signed certificates already stored on the server
taskd_client_cert: "{{ taskd_data_location }}/client.cert.pem"
taskd_client_key: "{{ taskd_data_location }}/client.key.pem"
taskd_server_cert: "{{ taskd_data_location }}/server.cert.pem"
taskd_server_key: "{{ taskd_data_location }}/server.key.pem"
taskd_server_crl: "{{ taskd_data_location }}/server.crl.pem"
taskd_ca_cert: "{{ taskd_data_location }}/ca.cert.pem"

# Set to false to disable the generation of selfsigned certificates
taskd_selfsigned: true

# Bits for SSL certificate (2048 or 4096)
taskd_selfsigned_bits: 4096
# Common Name for your certificate (MUST match hostname!)
taskd_selfsigned_cn: "{{ ansible_hostname }}"
# Days the certificate is valid for
taskd_selfsigned_expiration_days: 365
# Certificate organization
taskd_selfsigned_organization: Göteborg Bit Factory
# Certificate country
taskd_selfsigned_country: SE
# Certificate state
taskd_selfsigned_state: Västra Götaland
# Certificate locality
taskd_selfsigned_locality: Göteborg
```

## Dependencies

None

## Example Playbooks
----------------

It is better to move the variables (`taskd_users`, `taskd_organizations` etc.) to host_vars of your project but this will work.

#### Example Playbook with selfsigned SSL
The following playbook uses selfsigned ssl certificates which might not be the best idea for production use but it might be acceptable for personal use.
Please change the your.domain.tld to a valid value. This is the bare minimum to get started, the generated certificate will have the Swedish default localization. It is better to define most of the selfsigning variables for your locality.

    - hosts: taskservers    
      roles:
         - { role: wilmardo.taskserver, taskd_selfsigned_cn: your.domain.tld, 
                                        taskd_organizations: 'Public',
                                        taskd_users: [{name: 'First Last', organization: 'Public'}, {name: 'First Later', organization: 'Public'}] } 

#### Example Playbook with authorized SSL
The following playbook gives an example when using SSL certificates provided by a authority. The paths are an example please change to the location of your SSL certificates.

    - hosts: taskservers    
      roles:
         - { role: wilmardo.taskserver, taskd_selfsigned: false
                                        taskd_client_cert: /home/taskd/ssl/client.cert.pem, 
                                        taskd_client_key:/home/taskd/ssl/client.key.pem, 
                                        taskd_server_cert:/home/taskd/ssl/server.cert.pem, 
                                        taskd_server_key:/home/taskd/ssl/server.key.pem,  
                                        taskd_server_crl:/home/taskd/ssl/server.crl.pem, 
                                        taskd_ca_cert:/home/taskd/ssl/ca.cert.pem,
                                        taskd_organizations: 'Public',
                                        taskd_users: [{name: 'First Last', organization: 'Public'}, {name: 'First Later', organization: 'Public'}] } 

License
-------

BSD-3-Clause-Clear

Author Information
------------------

Wilmar den Ouden

https://wilmardenouden.nl
