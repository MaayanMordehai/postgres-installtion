# CONFIG PG OS USER
This role is configering the postgres os user to be by standart.

## Requirements

- Run on linux server, was tested only on redhat distribution.

## Expected result
The postgres os user define by standart with the right uid and gid and with our definitions for the bash profile.

## Parameters

| Name | Meaning | Default | Default definition location | Use cases that required change |
|:---|:---|:---|:---|:---|
| `pg_os_user` | The user name | `postgres` | defaults | When you want diffrent user | 
| `pg_os_group` | The primary group for the os user name | the same name as the `pg_os_user` | defaults | When you want diffrent primary group |
| `user_hashed_pass` | The user password hashed | not going to write here the password :) | defaults | When you want a diffrent password |
| `user_id` | the uid for the os user | `26` | defaults | When you want a diffrent uid |
| `group_id` | the gid for the group of the os user | `26` | defaults | When you want a diffrent gid |
| `pg_home_dir` | The home directory for the pg os user | `/var/lib/pgsql` | defaults | when you want a diffrent home |
| `user_shell` | the shell for the os user | `/bin/bash` | defaults | When you want a diffrent default shell |
| `pg_bash_profile_path` | the bash profile for the pg os user | `{{ home_dir }}/.pgsql_profile` | defaults | When you want a diffrent bash profile file, the default location is the file that the installtion rpms of adding to the original .bash_profile to read it, this way when there is an upgrade the rpms are not changing our definitions |
| `pg_data` | The pg data | `/postgres/{{ pg_major_version }}/data` | defaults | When you want a diffrent pg data. **When you don't specify this parameter you must specify the `pg_major_version` parameter.** |
| `pg_major_version` | the major version of the postgres software - for example `10`, `11` | - | - | you need to define this parameter only when you won't specify `pg_data` or `pg_bin`, and `pg_data` and `pg_bin` default value matches your environment |
| `pg_bin` | The location of the binaries of postgresql | `/usr/pgsql-{{ pg_major_version }}/bin` | When you have a diffrent location for binaries. **When you don't specify this parameter you must specify the `pg_major_version` parameter.** |


## Templates

| Name | Meaning |
|:---|:---|
| pgsql_profile.j2 | The bash profile for the postgres user |

## Tags

| Name | Meaning |
|:---|:---|
| pg-user | The definition of the `pg_os_user` and `pg_os_group`  |
| pg-bash-prof | Adding the bash profile |

## Examples

This will define the postgres user using all the default values, `pg_data` in this case is `/postgres/10/data`.
```yaml
- import_role:
    name: cfg-pg-os-user
  vars:
    pg_major_version: 10
```
---
This will define the postgres user using default values except `pg_data` that is `/postgres/data`, `pg_home_dir` that is `/home/postgres`, `user_id` will be `2634` and `group_id` will be `2635`.
```yaml
- import_role:
    name: cfg-pg-os-user
  vars:
    pg_data: /postgres/data
    pg_home_dir: /home/postgres
    user_id: 2634
    group_id: 2635
```

## Diffrent Network
You **might** want to change the uid and gid (`user_id` and `group_id`) in diffrent networks.

