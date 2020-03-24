# ADD KNOWN HOSTS
This role is adding hosts to the known hosts file.

## Requirements

- **Must** have gathered facts when not defining the `known_hosts` parameter.
- Run on linux server, was tested only on redhat distribution.

## Expected result
This role is adding the hosts (the names and the ips) in the `ansible_play_batch` which contains all the hosts that the play run on, in the batch. The ip is the IPv4 in eth0.

> If you want other host names or ips you should give the parameter `known_hosts`.

Adding the hosts to the known hosts file will allow the user running the role to authenticate the hosts.

## Parameters

| Name | Meaning | Default | Default definition location | Use cases that required change |
|:---|:---|:---|:---|:---|
| `known_hosts` | List of the ips and/or hostname you want to authenticate | `ansible_play_batch` and the ips | defaults - using ansible facts | when you need records of hosts/ip that the play doesn't run on (in the batch), or when you need some of the records of the hosts/ips that the play run on (in the batch), but not all of them. |

## Examples
This will add each of the hosts that are running in the batch and the ips to the known hosts file of user dba in the hosts in the batch.
```yaml
- import_role:
    name: add-known-hosts
  become_user: dba
```
---
This will add the hosts that are running in the batch to known host of host1 user dba.
```yaml
- import_role:
    name: add-known-hosts
  become_user: dba
  delegate_to: host1
```
---
This will add the hosts in the `known_hosts` to the known hosts file of the user running the play in hosts that the play run on.
```yaml
- import_role:
    name: add-known-hosts
  vars:
    known_hosts: 
      - "127.0.0.1"
      - "55.78.5.4"
      - "host1"
```

## Diffrent Network
There is no need to change anything in this role when moving to a diffrent network.
