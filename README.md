# ansible-role-fortiadc-create-zone

## Description

An Ansible role to create/update Global DNS Zones and their DNS entries on FortiADC devices via REST API.

## Roadmap

- [x] create/update root GLB Hosts for domain to automatically create fqdn_generate zones (I did this since you can't change Primary Zones to FQDN Generate Zones, and you can't use GLB Hosts on existing Primary Zones)
- [x] move the zones into DNS Policy 
- [x] create/update A/AAAA Records
- [x] create/update NS Records
- [x] create/update PTR Records
- [x] create/update CNAME Records
- [x] create/update TXT records
- [ ] create/update CAA records
- [ ] create/update MX records

(yeah, this shouldn't be version 1.0.0, I messed up)

## Usage

### Install Role

#### From Galaxy

```
ansible-galaxy install ndkprd.fortiadc-dns-policy
```

#### From Github

##### Create Requirements File

```
---
# ./requirements.yaml

- name: ndkprd.fortiadc-dns-zones
  scm: git
  src: https://github.com/ndkprd/ansible-role-fortiadc-dns-zones.git
  version: main # or 'devel' or release/tag name
```

##### Install

```
ansible-galaxy install -r requirements.yaml
```

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
        a_aaaa_records:
          - hostname: ns # hostname
            id: "1001" # high ID to be used as mkey
            ipv4: "10.10.10.2" # ip to be resolved to
            ipv6: "::"
            ttl: "-1" # time-to-live, will inherit zone if "-1"
            source_type: "ipv4" # or ipv6
        ns_records: []
        ptr_records: []
        cname_records: []
        txt_records: []
        caa_records: []
        mx_records: []


  roles:
    - role: fortiadc-dns-zones
```

### Hosts Example

```

[fortiadc]
fad1 ansible_host=fad1.infra.ndkprd.com fad_apitoken=mysupersecrettoken1 fad_vdom=root
fad2 ansible_host=fad2.infra.ndkprd.com fad_apitoken=mysupersecrettoken2 fad_vdom=root
fad3 ansible_host=fad3.infra.ndkprd.com fad_apitoken=mysupersecrettoken3 fad_vdom=root

```

### About Tags

I added quiet lots of debug task, mainly to check if the variable I set is correct. These tags basically just print out the var that the previous task set/register. You can skip them altogether by skipping tasks with `debug` tags.

For example, if you're using CLI, you can just go `ansible-playbook playbook.yaml --skip-tags debug`.

## Limitation

Developed and so far only tested against Hardware FortiADC with firmware 7.0.

## License

MIT License, use at your own risk.
