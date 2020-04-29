# setup_rhel_for_satellite

Ansible collection for validating a RHEL host to run satellite.

This collection is also available on [ansible galaxy](https://galaxy.ansible.com/jjaswanson4/setup_rhel_for_satellite).

## Overview
This collection will validate a RHEL7 host against the [system requirements](https://access.redhat.com/documentation/en-us/red_hat_satellite/6.7/html/installing_satellite_server_from_a_connected_network/preparing-environment-for-satellite-installation#system-requirements_satellite) and [storage requirements](https://access.redhat.com/documentation/en-us/red_hat_satellite/6.7/html/installing_satellite_server_from_a_connected_network/preparing-environment-for-satellite-installation#satellite-storage-requirements_satellite) for running satellite.

If a system passes validation, LVM will be configured according to the following procedure:
- LVs will be created
- The LVs will get an XFS filesystem
- The LVs will be added to /etc/fstab

* Note: for now, the LVs will be on the same volume group as /var. There is an [issue](https://github.com/jjaswanson4/setup_rhel_for_satellite/issues/1) open on github about this.

## Usage
The best way to consume this collection is to set up a requirements.yml:
```yaml
---
collections:
  - jjaswanson4.setup_rhel_for_satellite
```
The collection can also be installed from the command line ad-hoc:
```
ansible-galaxy collection install jjaswanson4.setup_rhel_for_satellite
```

## Examples
- There are example playbooks located in playbooks directory
- Vars examples can be found in playbooks/vars

## Vars
```yaml
satellite:
  version: 6.7
  admin_username: admin
  admin_password: changeme
  logical_volumes:
    - name: cache # The name of the LV
      size: 20 # Size in GB
      path: /var/cache/pulp # Mount path
    - name: pulp
      size: 300
      path: /var/lib/pulp
    - name: mongodb
      size: 50
      path: /var/lib/mongodb
    - name: qpidd
      size: 20
      path: /var/lib/qpidd
    - name: varlog
      size: 10
      path: /var/log
    - name: pqsql
      size: 25
      path: /var/lib/pgsql
    - name: squid
      size: 10
      path: /var/spool/squid
    - name: usr
      size: 8
      path: /usr
    - name: opt
      size: 8
      path: /opt
    - name: puppetlabs
      size: 1
      path: /opt/puppetlabs
    - name: var
      size: 12
      path: /var
```

## Inventory Structure
For this collection, it doesn't matter if you're building a satellite server or a capsule server, the LVs will be collected and created. It's a good idea to break out satellite servers vs. capsule servers and specify their var files so each will be built according to their own specifications:
```yaml
[satellite]
satellite67.josh.lab.msp.redhat.com vars_file=/home/jswanson/ansible/satellite6.7-collections/satellite67.josh.lab.msp.redhat.com.vars.yml ansible_user=root

[capsule]
capsule67-01.josh.lab.msp.redhat.com vars_file=/home/jswanson/ansible/satellite6.7-collections/capsule67-01.josh.lab.msp.redhat.com.vars.yml ansible_user=root
capsule67-02.josh.lab.msp.redhat.com vars_file=/home/jswanson/ansible/satellite6.7-collections/capsule67-02.josh.lab.msp.redhat.com.vars.yml ansible_user=root
```
