# ansible-role-fortiadc-create-zone

## Description

An Ansible role to create DNS zone on FortiADC devices.

## Usage

### Playbook Example

```
---
# ./playbook.yaml

- name: Create new FortiADC zone.
  hosts: fortiadc
  become: true
  gather_facts: no
  vars:
    fad_vdom: "root"
    fad_dns_policy:
      - name: "DEFAULT_DNS_POLICY" # Global DNS Policy mkey
        source_address: "any" # valid Address Group entry mkey used as source
        destination_address: "any" # valid Address Group entry used as destination
        dns64_list: ""
        dnssec_validate_status: "disable" # "enable" or "disable"
        forward: "first" # "first" or "only"
        forwarders: "" # valid Remote DNS Servers entry mkey
        recursion_status: "disable" # "enable" or "disable"
        rrlimit: "" # valid Response Rate Limit 
    fad_dns_zones:
      - name: "infra.ndkprd.com" # base domain without dot, will be converted to the mkey by task
        dns_policy: "DEFAULT_DNS_POLICY" # valid DNS Policy mkey
        ttl: "86400" # zone's time-to-live
        negative_ttl: "3600" # zone's negative time-to-live
        primary_ns_ipv4: "10.10.10.2" # primary IPv4 nameserver
        primary_ns_ipv6: "::" # primary IPv6 nameserver
        primary_ns_name: "ns" # primary nameserver hostname
        responsible_mail: "admin" #responsible mail, use dot if include domain
        allow_transfer: "" # valid Address Group mkey
        a_aaaa_record:
          - hostname: ns # hostname
            id: "1001" # high ID to be used as mkey
            ipv4: "10.10.10.2" # ip to be resolved to
            ipv6: "::"
            ttl: "-1" # time-to-live, will inherit zone if "-1"
            source_type: "ipv4" # or ipv6

  roles:
    - role: fortiadc-create-zone
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

## License

MIT License, use at your own risk.
