# CREATE PG CLUSTER
This role is initiating postgres cluster.

## Requirements

- Run on linux server, was tested only on redhat distribution.
- Postgres-server rpms must be installed.
- The postgres service should define the PGDATA as `pg_data`.

## Expected result

The `pg_data` will be initiated with the postgres cluster files and the new cluster will be started.

## Parameters

| Name | Meaning | Default | Default definition location | Use cases that required change |
|:---|:---|:---|:---|:---|
| `pg_bin` | The postgresql binaries location | `/usr/pgsql-{{ pg_major_version }}/bin` | defaults | When you have diffrent binaries location |
| `pg_data` | The postgresql data location | `/postgres/{{ pg_major_version }}/data` | defaults | When you have diffrent data location |
| `pg_major_version` | The major version of postgres that is in use | - | - | When you don't overwrite `pg_srv_name`, `pg_bin` and `pg_data` you need to define this |
| `pg_srv_name` | The postgresql service name | `postgresql-{{ pg_major_version }}.service` | defaults | When you have diffrent service name - won't happen normaly |

## Tags

| Tag | Meaning |
|:---|:---|
| initdb | The initiation of the `pg_data` with cluster files |

## Diffrent Network
There is no need to change anything in this role when moving to a diffrent network.
