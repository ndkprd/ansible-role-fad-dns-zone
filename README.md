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
  vars_files:
    - vars.yaml

  roles:
    - fortiadc-create-zone
```

### Hosts Example

```
[fortiadc]
fad1 ansible_host=fad1.infra.ndkprd.com fad_apitoken=mysupersecrettoken1 fad_vdom=root
fad2 ansible_host=fad2.infra.ndkprd.com fad_apitoken=mysupersecrettoken2 fad_vdom=root
fad3 ansible_host=fad3.infra.ndkprd.com fad_apitoken=mysupersecrettoken3 fad_vdom=root

[fortiadc:vars]
fad_http_port=80
fad_https_port=443

```

### Needed Variables Example

```
---
# ./vars/main.yaml

# GLOBAL-LOAD-BALANCE ZONE
fad_domains:
  - domain_name: infra.ndkprd.com # base domain without dot at the end
    domain_scope: public # scope for naming only, I personally use "public" and "local"
    ttl: 86400 
    negative_ttl: 3600 
    primary_ns_ip: 10.10.10.1
    primary_ns_name: ns
    responsible_mail: admin.ndkprd.com.
  - domain_name: devops.ndkprd.com
    domain_scope: public
    ttl: 86400 
    negative_ttl: 3600
    primary_ns_ip: 10.10.10.1
    primary_ns_name: ns
    responsible_mail: admin.ndkprd.com.

# GLOBAL-DNS-SERVER POLICY
fad_dns_policy:
  name: DEFAULT_DNS_POLICY # name of the DNS policy used by FAD

#GLOBAL-DNS-SERVER A RECORDS
zone_aaa_records:
  # devops.ndkprd.com
  - hostname: ns
    domain_name: devops.ndkprd.com
    ip: 10.10.10.1
    mkey: 1001 # high number of mkey to make sure it's not conflicted
  # infra.ndkprd.com
  - hostname: ns
    domain_name: infra.ndkprd.com
    ip: 10.10.10.1
    mkey: 1001

#GLOBAL-DNS-SERVER NS RECORD
zone_ns_records:
  # devops.asdp.id
  - hostname: ns
    domain_name: devops.ndkprd.com
    ip: 36.92.152.200
    mkey: 1001
  #devsecops.asdp.id:
  - hostname: ns
    domain_name: infra.ndkprd.com
    ip: 36.92.152.200
    mkey: 1001

```

## License

MIT License, use at your own risk.
