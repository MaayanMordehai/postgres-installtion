# CONFIG NO SELINUX
This role is configuring selinux to be on permissive or disabled.

> This role includes reboot handler. if you don't want reboot you should give the `wanted_se_state` parameter as permissive

## Requirements

- Run on linux server, was tested only on redhat distribution.
- Run as root.

## Expected result
selinux disabled or permissive (default disabled).

## Parameters

| Name | Meaning | Options |  Default | Default definition location | Use cases that required change |
|:---|:---|:---|:---|
| `wanted_se_state` | the wanted selinux state | disabled / permissive | disabled | defaults | when you want selinux as permissive |

## Handlers

Reboot handler that runs if selinux state was changed to disabled.

## Examples
This will disable selinux on all the play hosts
```yaml
- import_role:
    name: cfg-no-selinux
```
---
This will make selinux in permissive
```yaml
- import_role:
    name: cfg-no-selinux
  vars:
    wanted_se_state: "permissive"
```

## Diffrent Network
There is no need to change anything in this role when moving to a diffrent network.
