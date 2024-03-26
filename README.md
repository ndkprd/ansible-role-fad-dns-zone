# ansible-role-fortiadc-create-zone

## Description

An Ansible role to create DNS zone on FortiADC devices.

## Usage

### Playbook Example

```
---
# ./playbook.yaml

- name: Create new FortiADC zone.
  hosts: all
  become: true
  gather_facts: no

  roles:
    - fortiadc-create-zone
```

### Host Example

```
[fortiadc]
fad1 ansible_host=fad1.infra.ndkprd.com fad_apitoken=mysupersecrettoken1 fad_vdom=root
fad2 ansible_host=fad2.infra.ndkprd.com fad_apitoken=mysupersecrettoken2 fad_vdom=root
fad3 ansible_host=fad3.infra.ndkprd.com fad_apitoken=mysupersecrettoken3 fad_vdom=root

[fortiadc:vars]
fad_http_port=80
fad_https_port=443

```

## License

MIT License, use at your own risk.
