# CONFIG PG SERVICE
This role is configuring the service of postgresql.

## Requirements

- Systemd.
- Run on linux server, was tested only on redhat distribution.

## Expected result

When the postgres service will run, it will be with the PGDATA as in the `pg_data`, the service will be enabled.

## Parameters

| Name | Meaning | Default | Default definition location | Use cases that required change |
|:---|:---|:---|:---|:---|
| `pg_data` | The postgresql data location | `/postgres/{{ pg_major_version }}/data` | defaults | when you have diffrent data location |
| `pg_major_version` | The major version of postgres that is in use | - | - | When you don't overwrite `pg_srv_name` and `pg_data` you need to define this |
| `pg_srv_name` | The postgresql service name | `postgresql-{{ pg_major_version }}.service` | defaults | When you have diffrent service name - won't happen normaly |
| `pg_srv_path` | The path to the service file location | `/usr/lib/systemd/system` | defaults | When the service file is in a diffrent location |

## Diffrent Network
There is no need to change anything in this role when moving to a diffrent network.
