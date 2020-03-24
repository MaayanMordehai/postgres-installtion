# PREP-DISK-APP

This role is mapping the oracle partitions for APP database
We are getting from the APP a server with only one disk which will be divided to Partitions.
Partitions 1-5 are for the ASM and should be mapped properly.

> This role is not in use in normal cases.
> It was made specificly for APP.

## Expected Result
The disk partitions are mapped properly for oracle ASM. The mapping can be seen in `/dev/mapper`.
If running with default values:
```shell
ll /dev/mapper
control
ora_asm_data1p1 -> ../sdb1
ora_asm_fra1p1 -> ../sdb2
ora_asm_grid1p1 -> ../sdb3
ora_asm_grid2p1 -> ../sdb4
ora_asm_grid3p1 -> ../sdb5
```
also permissions on the partitions should be oragrid, asmadmin


## Parameters

| Name | Default | Default definition location | Use cases that required change |
|:---|:---|:---|:---|
| `app_disk` | `/dev/sdb` | Defaults folder | When the asm disk is not /dev/sdb |
| `app_partitions_and_dest` | `{ "1": "data1p1", "2": "fra1p1", "3": "grid1p1", "4": "grid2p1", "5": "grid3p1" }` | When the partitions are not 1 - data, 2 - fra, 3-5 grid. or when you have more partitions of data/fra. |
| `ora_type` | `RESTART` | Vars folder | this role does not support RAC because the APP does not need RAC. so never change this |
| `disk_uuid.stdout`| the uuid of `app_disk` | at runtime using command and register | probably never |

## Handlers

| Name | Meaning |
|:---|:---|
| udevadm trigger | reloading udev roles - for the new mapping to occur |

## Template

| name | Meaning |
|:---|:---|
| 20-asm-permission-APP-RESTART-rules.j2 | the rules for asm | 

## Examples

Running with default values.
```yaml
- import_role:
    name: prep-disks-app
```
---
Running with diffrent disk.
```yaml
- import_role:
    name: prep-disks-app
  vars: 
    app_disk: "/dev/sdc"
```
---
Running with diffrent partitions.
```yaml
- import_role:
    name: prep-disks-app
  vars:
    app_partitions_and_dest: { "6": "grid1p1", "7": "grid2p2", "3": "data1p1", "5": "fra1p1", "1": "grid1p1" }
```


