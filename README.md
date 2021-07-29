# DEPLOY POSTGRESQL
This was created for diffrent network and not internet.

This deployment is divided to playbooks. We have the master play and the childs that run after.
The playbooks flow is this: 
- Master play check the reqiured parameters for the other plays
- Child play prerequisite prepere our servers for the cluster and replica deployments
- if `pg_type: REPLICA`
  - Child play deploy cluster run only on the master.
  - Child play deploy replica run on the servers.
- else
  - Child play deploy cluster run on all given hosts.

## Mandatory Parameters
The parameters listed in here are the mandatory parameters.

Needed for both standalone and replica deployments:

| Parameter | Explain | Used by plays |
|:---|:---|:---|
| `app_db_name` | The application database name | deploy_cluster |
| `app_user_name` | The application user name | deploy_cluster |
| `app_user_pass` | The password of the app user - should be sent after md5 hashed (will work either way) | deploy_cluster |
| `cluster_name` | This parameter is the inventory group name - also the hosts name without the end number | deployment, prerequisite, deploy_cluster (only when `pg_type: STANDALONE` ), deploy_replica |
| `pg_type` | This parameter is the type of postgresql deployment - STANDALONE / REPLICA | deployment, prerequisite |

Needed parameters for only replica deployments:

| Parameter | Explain | Used by plays |
|:---|:---|:---|
| `primary_host` | The host in the inventory group `cluster_name` that will be the master of the replica | deploy_cluster, deploy_replica |

