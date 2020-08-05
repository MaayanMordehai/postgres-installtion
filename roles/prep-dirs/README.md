# PREP DIRS
This role is prepering directories - adding disk to lvm, creating fs - xfs, mounting to base directory and creating sub directories.

## Requirements

- Run on linux server, was tested only on redhat distribution.

## Expected result

The `fs_disk` will have a partiton 1 with all that is free type lvm, the partition will be in the `fs_vg` - and the only one there. also There will be lv named `fs_lv` and a fs - xfs will be created. the lv will be mounted to `base_dir` and directories will be created according to the information in `dirs_info`.
> The size of the volume will be +100%FREE from the vg


## Parameters

| Name | Meaning | Default | Default definition location | Use cases that required change |
|:---|:---|:---|:---|:---|
| `fs_disk` | The disk to use for the new fs | `/dev/sdc` | defaults | When you want a diffrent disk to be used |
| `fs_vg` | The vg to put the disk on - should be the only disk that will be there | `vg01` | defaults | When vg01 exsist and contain disks which are not `fs_disk` or when you want a diffrent vg name |
| `fs_lv` | The lv name  | `lolv` | defaults | When you want a diffrent name for the lv or when there is an lv with this name that is ment for other purpose |
| `base_dir` | The directory that the lv will be mounted to | - | - | - |
| `dirs_info` | List of dictionaries of the info for the directories to create, the info include `path`, `mode`, `owner`. for example: <pre>dirs_info: <br>  - path: /base/dir/first <br>    owner: dba <br>    mode: 0700 <br>  - path: /base/dir/second <br>    owner: dba <br>    mode: 0764 <br> </pre> | `{}` | defaults | When you want to create directories |

## Tags

| Name | Meaning |
|:---|:---|
| cre-patitions | The creation of the partition 1 of type lvm on `fs_disk` |
| lvm | the creation of the vg and the lv |
| cre-lv | The creation of the `fs_lv` in the `fs_vg` |
| fs | The creation of the fs on the `fs_lv` in the `fs_vg` |
| mount | The mount to the `base_dir` |
| sub-dirs | The creation of the directories according to `dirs_info` |

## Examples

This will take the `/dev/sdc` disk, create partition 1, add it to the lvm to vg01, lovl, won't create more directory
```yaml
- import_role:
    name: prep-dirs
  vars:
    base_dir: /dba
```
---
This will take the `/dev/sdd` disk, create partition 1, add it to the lvm to myvg, mylv, and create the directories with the permissions.
for the /lala dir it will change permissions according to the dirs_info
```yaml
- import_role:
    name: prep-dirs
  vars:
    fs_disk: /dev/sdd
    fs_vg: myvg
    fs_lv: mylv
    base_dir: /lala
    dirs_info:
      - path: /lala
        owner: dba
        mode: 0700
      - path: /lala/dirA
        owner: dba
        mode: 0700
      - path: /lala/dirA/dirC
        owner: dba
        mode: 0755
      - path: /lala/dirB
        owner: root
        mode: 0777
```

## Diffrent Network
There is no need to change anything in this role when moving to a diffrent network.
