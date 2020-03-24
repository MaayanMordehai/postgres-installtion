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

The parameters listed in here are the mandatory parameters.
The unmandatory parameters can be seen in the childs section.

Needed for both standalone and replica deployments:

| Parameter | Explain | Used by plays |
|:---|:---|:---|
| `cluster_name` | This parameter is the inventory group name - also the hosts name without the end number | deployment, prerequisite, deploy_cluster (only when `pg_type: STANDALONE` ), deploy_replica |
| `pg_type` | This parameter is the type of postgresql deployment - STANDALONE / REPLICA | deployment, prerequisite |
| `version` | The full version of the postgresql to install major.minor | deployment, prerequisite, deploy_cluster, deploy_replica |
| `app_db_name` | The application database name | deploy_cluster |
| `app_user_name` | The application user name | deploy_cluster |
| `app_user_pass` | The password of the app user - should be sent after md5 hashed (will work either way) | deploy_cluster |

Needed parameters for only replica deployments:

| Parameter | Explain | Used by plays |
|:---|:---|:---|
| `master_host` | The host in the inventory group `cluster_name` that will be the master of the replica | deploy_cluster, deploy_replica |

## CHILD PLAYBOOK - PREREQUISITE

This playbook is doing all the prerequisite for initiating postgresql cluster or replica.

### Roles

TODO
