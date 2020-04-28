# CONFIG SSH NOPASS
This role is configuring ssh-connectivity without password from the current user, current host to the hosts and users in `dest_hosts_users_nopass`.

## Requirements

- Run on linux server, was tested only on redhat distribution.
- The user running this role should have rsa public key on the host this role is running on, in the default location - in his homedir in the .ssh directory.

## Expected result

The running user on the running host will be able to connect without password to the users and hosts as listed in `dest_hosts_users_nopass`.

## Parameters

| Name | Meaning | Default | Default definition location | Use cases that required overwrite |
|:---|:---|:---|:---|:---|
| `dest_hosts_users_nopass` | List of dictionaries with hosts and users (the keys are `host`, `user`) to be able to connect to without password | `[]` | defaults | if you won't define this parameter this role won't do anything, so Always |
| `pub_key` | the user public key b64encoded | no default - it is taken from the rsa public key file | In the role as a register for the public key file | there is no reason to overwrite this |

> example for `dest_hosts_users_nopass` parameter definition can be seen in the examples section


## Examples

This will allow the running user - dba to connect with no password from the hosts that this is run on to user myuser in host1 and to user dba in host2.
```yaml
- import_role:
    name: cfg-ssh-nopass
  vars:
    dest_hosts_users_nopass:
      - user: myuser
        host: host1
      - user: dba
        host: host2
  become_user: dba
```
---
This will allow the running user - root to connect with no password from hostA to user myuser in host1.
```yaml
- import_role:
    name: cfg-ssh-nopass
  vars:
    dest_hosts_users_nopass:
      - user: myuser
        host: host1
  become_user: root
  delegate_to: hostA
```

## Diffrent Network
There is no need to change anything in this role when moving to a diffrent network.
