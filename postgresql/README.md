# DEPLOY POSTGRESQL REPLICA BY STANDART

This deployment is divided to playbooks. We have the master playbook that runs all others.

## MASTER PLAYBOOK - DEPLOYMENT

This playbook run all other playbooks the way we need to create a standart replication.

### Parameters

The parameters listed in here are importent to the deploy play itself - and not the childs.
The childs parameters can be seen in their section.

| Parameter | Explain |
| --- | --- |
| `cluster_name` | This parameter is the inventory group name - also the hosts name without the end number |
 

## CHILD PLAYBOOK - 
