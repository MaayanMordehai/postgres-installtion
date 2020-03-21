# CONFIG HOSTS FILE
This role is adding hosts to the known hosts file.

## Requirements

- **Must** have gathered facts when not defining the `hosts_records` parameter.
- Run on linux server, was tested only on redhat distribution.
- Run as root.

## Expected result
This role is adding the hosts in the `ansible_play_batch` which contain all the hosts that the play run in a batch.
The host are added with both the domain and without it. 
The ip of the hosts is the IPv4 in eth0.

> If you want other hosts you should give the parameter `hosts_records`.

## Parameters

| name | default | default defined in | use cases for change |
|:---:|:---:|:---:|:---:|
| `hosts_records` | the ip, hostname and hostname with domain of the hosts that are running in the batch | set fact - on run time using facts | when you need records of hosts that the play not run on, or when you don't need the records of all the hosts that the play rum on.

## Examples
This will add each of the hosts that are running in the batch to the hosts in this batch.
```ymal
- import_role:
    name: cfg-hosts-file
```
---
This will add the hosts that are running in the batch to host1.
```ymal
- import_role:
    name: cfg-hosts-file
  delegate_to: host1
```
---
This will add the hosts in the `hosts_records` to the hosts that the play run on.
```ymal
- import_role:
    name: cfg-hosts-file
  vars:
    hosts_records:
      - { ip: 127.0.0.1, hostname: localhost }
      - { ip: 55.78.5.4, hostname: host1, hostname_with_domain: host1.domain }
```
