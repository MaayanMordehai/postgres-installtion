# DEPLOY POSTGRESQL REPLICA BY STANDART

This deployment is divided to playbooks. We have the master play and the childs that run after.
The playbooks flow is this: 
- Master play check the reqiured parameters for the other plays
- Child play prerequisite prepere our servers for the cluster and replica deployments
- if `pg_type: REPLICA`
  - Child play deploy cluster run only on the master.
  - Child play deploy replica run on the servers.
- else
  - Child play deploy cluster run on all given hosts.

## MASTER PLAY - DEPLOYMENT

This play check the variables we are getting from the user - to make sure we have the reqiured paramters.

### Parameters

#### Mandatory Parameters
The parameters listed in here are the mandatory parameters.

Needed for both standalone and replica deployments:

| Parameter | Explain | Used by plays |
|:---|:---|:---|
| `app_db_name` | The application database name | deploy_cluster |
| `app_user_name` | The application user name | deploy_cluster |
| `app_user_pass` | The password of the app user - should be sent after md5 hashed (will work either way) | deploy_cluster |
| `cluster_name` | This parameter is the inventory group name - also the hosts name without the end number | deployment, prerequisite, deploy_cluster (only when `pg_type: STANDALONE` ), deploy_replica |
| `pg_type` | This parameter is the type of postgresql deployment - STANDALONE / REPLICA | deployment, prerequisite |
| `version` | The full version of the postgresql to install major.minor | deployment, prerequisite, deploy_cluster, deploy_replica |

Needed parameters for only replica deployments:

| Parameter | Explain | Used by plays |
|:---|:---|:---|
| `primary_host` | The host in the inventory group `cluster_name` that will be the master of the replica | deploy_cluster, deploy_replica |

#### Unmandetory Parameters

| Name | Meaning | Default | Default definition location | Use cases that required overwrite | Used by plays | 
|:---|:---|:---|:---|:---|:---|
| `admin_user_pass` | The db admin user password | protected with ansible-vault | group_vars in the play | You should not overwrite this - change only in the code itself | deploy_cluster |
| `base_dir` | The directory of the version we install, it is the base directory for postgresql directories in our standart (inside we will have the data dir, tbs and such). | `{{ postgresql_dir }}/{{ pg_major_version }}` | group_vars in the play | When you want diffrent base dir | prerequisite **TODO** | 
| `dirs_info` | directories to create for the postgresql and their information - `owner`, `mode`, `path` | <pre>dirs_info: <br>  - path: "{{ postgresql_dir }}" <br>    owner: "{{ pg_os_user }}" <br>    mode: 0774 <br>  - path: "{{ base_dir }}" <br>    owner: "{{ pg_os_user }}" <br>    mode: 0774 <br>  - path: "{{ pg_data }}" <br>    owner: "{{ pg_os_user }}" <br>    mode: 0700 <br>  - path: "{{ pg_tbs }}" <br>    owner: "{{ pg_os_user }}" <br>    mode: 0700 <br>  - path: "{{ pg_wals_from_pri }}" <br>    owner: "{{ pg_os_user }}" <br>    mode: 0700 <br>  </pre> | group_vars in the play | you should not change this, if you want something diffrent change the specific parameter | prerequisite |
| `fs_lv` | The lv name  | `pglv` | group_vars in the play | When you want a diffrent name for the lv |
| `fs_vg` | The vg that will be created for the postgresql volume | `vgpg` | group_vars in the play | When you want a diffrent vg name, **don't use vg that need to be for other purpose, should be new vg only for this** | prerequisite |
| `pg_admin_user` | The postgresql admin user | `postgres` | group_vars in the play | Should not change most cases | deploy_cluster |
| `pg_bin` | The location of the postgresql binaries files | `/usr/pgsql-{{ pg_major_version }}/bin` | group_vars in the play | When binaries location is diffrent - which is probably never | prerequisite **TODO** | 
| `pg_data` | The pg data | `{{ base_dir }}/data` | group_vars in the play | when you want to define diffrent pg data | prerequisite, deploy_cluster, deploy_replica |
| `pg_db` | The admin postgres database | `postgres` | group_vars in the play | Should not change most cases | deploy_cluster |
| `pg_full_version` | The full postgres version | The same as the `version` parameter | group_vars in the play | you should not define this defined as the `version` parameter - you should define it | prerequisite |
| `pg_major_version` | Postgres major version to install | According to the value of `version` in extra vars - only the major | group_vars in the play | you should never change this, only define the `version` currectly | prerequisite, deploy_cluster, deploy_replica |
| `pg_os_user` | The postgres admin os user | `postgres` | group_vars in the play | When you want a diffrent user - mostly no need to change | prerequisite, deploy_cluster, deploy_replica |
| `pg_srv_name` | Postgresql service name | `postgresql-{{ pg_major_version }}.service` | group_vars in the play | You should not change this parameter usually | prerequisite, deploy_replica **TODO** |
| `pg_stat_statement_extention` | dictionary of the extention installtion info - the database to apply the extention on, if any db parameters are needed and such | <pre>pg_stat_statement_extention: <br>  extention: "pg_stat_statements" <br>  database: "{{ pg_db }}" <br>  restart_params: <br>    - name: "shared_preload_libraries" <br>      value: "pg_stat_statements" <br>    reload_params: <br>    - name: "pg_stat_statements.max" <br>      value: "1000" <br>    - name: "pg_stat_statements.track" <br>      value: "all" <br> </pre> | group_vars in the play | If you want diffrent values for the parameters or more parameters | deploy_cluster |
| `pg_tbs` | The postgresql tablespaces location | `{{ base_dir }}/tbs` | group_vars in the play | When you want a diffrent tablespaces directory | prerequisite, deploy_cluster |
| `pg_wals_from_pri` | The location for wals for recovery when there is a lag | `{{ base_dir }}/wals_from_primary` | group_vars in the play | When you want a diffrent location | prerequisite, deploy_replica | 
| `postgis` | boolean, yes - add postgis extention, no - don't add postgis extention | `no` | group_vars in the play | When you want postgis extention | prerequisite, deploy_cluster |
| postgis_extention | dictionary of the extention installtion info - the database to apply the extention on, if any db parameters are needed and such | <pre>postgis_extention: <br>  extention: "postgis" <br>  database: "{{ app_db_name }}" <br>  </pre> | group_vars in the play | When you want diffrent databases to add the extention to, when you need to define parameters regarding to the extention and such, you can see what is the names of the keys allowed in [add-extention readme](./roles/add-extention/README.md) | deploy_cluster |
| `postgresql_dir` | The postgresql main directory which inside will have the versions specific directories | `/postgres` | group_vars in the play | When you want diffrent directory | prerequisite **TODO** |
| `rep_user_name` | The name of the replication user | `replicator` | group_vars in the play | When you want diffrent replication user | deploy_replica |
| `rep_user_pass` | The replication user password | protected with ansible-vault | group_vars in the play | You should not overwrite this - change only in the code itself | deploy_replica |
| `replica_required_params` | A list of the replication parameter names which are required - those are parameters that are required only by the deploy_replica playbook | <pre>replica_required_params: <br>  - "primary_host" <br> </pre> | group_vars in the play | **Don't overwrite this !!!** | deployment |
| `required_params` | The required parameters that are needed for the plays (except parameters that are needed only for replica) | <pre>required_params: <br>  - "cluster_name" <br>  - "version" <br>  - "version" <br>  - "pg_type" <br>  - "app_db_name" <br>  - "app_user_name" <br>  - "app_user_pass"  <br> </pre> | group_vars in the play | **Don't overwrite this !!!** | deployment |
| `rmt_user` | The user to connect to the server with, need to be sudoer | `dba` | group_vars in the play | When you don't have this user as sudoer on the servers | deployment, prerequisite, deploy_cluster, deploy_replica |

TODO 

## CHILD PLAYBOOK - PREREQUISITE

This playbook is doing all the prerequisite for initiating postgresql cluster or replica.

### Roles

#### [cfg-no-selinux](./roles/cfg-no-selinux)

Disable selinux.
There are no parameters for this role that are defined in the play itself and not in the role, keeping the role values for the role parameters.

#### [cfg-pg-os-user](./roles/cfg-pg-os-user)

Define pg user.
The parameters that are defined in the play that are also in this role:
- `pg_data`
- `pg_major_version`
- `pg_os_user`

TODO  

#### [cfg-hosts-file](./roles/cfg-hosts-file)

TODO

#### [add-known-hosts](./roles/add-known-hosts)

TODO

#### [cfg-ssh-nopass](./roles/cfg-ssh-nopass)

TODO

#### [prep-dirs](./roles/prep-dirs) 

TODO

#### [inst-pg-rpms](./roles/inst-pg-rpms)

TODO

#### [cfg-pg-srv](./roles/cfg-pg-srv)

TODO

## CHILD PLAYBOOK - DEPLOY CLUSTER

TODO

## CHILD PLAYBOOK - DEPLOY REPLICA

TODO
