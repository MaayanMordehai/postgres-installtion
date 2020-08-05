# INST PG RPMS
This role is installing the postgres related rpms.

## Requirements

- The repos should be enabled before this role is run. The only repo that can be not enabled is the postgres repo, make sure that the `pg_repo_info` is correct for you.
- Run on linux server, was tested only on redhat distribution.

## Expected result
The postgresql rpms installed, the python extentions for ansible pg modules installed and if `postgis` is `yes` the postgis rpms installed.

## Parameters

| Name | Meaning | Default | Default definition location | Use cases that required overwrite |
|:---|:---|:---|:---|:---|
| `pg_full_version` | The full pg version you want to install - if you want the latest of the major version - just send the it as a major version | - | - | This is a **MUST** parameter |
| `pg_major_version` | the major version of postgres you want to install | `{{ ((pg_full_version\|string).split('.'))[0] }}` | vars | You should not overwrite this parameter, you should send the `pg_full_version` parameter instead |
| `pg_repo_info` | Dictionary of postgresql repo information, `name`, `base_url` and `desc` (description) | <pre>pg_repo_info: <br>  name: "pg{{ pg_major_version }}" <br>  base_url: http://location/of/repo/dependes/on/network <br>  desc: "Postgresql Repo - version {{ pg_major_version }}" <br></pre> | vars | when you want diffrent repo - most likly on diffrent network (in a diffrent network you can change in the code or overwrite the parameter) |
| `pg_rpms` | List of the postgresql rpms | <pre>pg_rpms: <br>  - "postgresql{{ pg_major_version }}-libs-{{ pg_full_version }}" <br>  - "postgresql{{ pg_major_version }}-server-{{ pg_full_version }}" <br>  - "postgresql{{ pg_major_version }}-contrib-{{ pg_full_version }}" <br>  - "pg_top{{ pg_major_version }}" <br>  - "pgcenter" <br></pre> | vars | when you want diffrent postgres rpms - probably never, because if the rpms you want changed you probably should change the code itself | 
| `postgis` | Boolean, should the postgis extention be installed or not | `no` | defaults | when you want postgis extention rpms |
| `postgis_repos` | The repos that are needed for the postgis rpms seperated by comma | `epel,rhel-7-server-rpms,{{ pg_repo_info.name }}` | vars | when you need diffrent repos for the postgis rpms - most likly on diffrent network (in a diffrent network you can change in the code or overwrite the parameter) |
| `postgis_rpms` | List of postgis rpms | `postgis{{ postgis_version }}_{{ pg_major_version }}` (when there is only one rpm it is ok its not as a list) | vars | When you want diffrent rpm for postgis - probably never |  
| `postgis_version` | The postgis version to install | `30` | defaults | when you want diffrent postgis version |
| `python_postgres_repos` | The repos that are needed for the `python_postgres_rpms` | `rhel-7-server-rpms,epel,rhui-rhel-7-server-rhui-eus-rpms,{{ pg_repo_info.name }}` | vars | when you need diffrent repos for the `python_postgres_rpms` - most likly on diffrent network (in a diffrent network you can change in the code or overwrite the parameter) |
| `python_postgres_rpms` | Rpms of the python extentions needed to run postgresql modules in ansible | <pre>python_postgres_rpms: <br>  - "python-psycopg2" <br>  - "python-ipaddress" <br></pre> | vars | you should not overwrite this, if you need extra module add it to the code |
| `repos_file_name` | The file name for the repo where the postgresql rpm will be enabled - without repo end | `dba` | defaults | When you want diffrent file for this repo, note that the pg repo (as in `pg_repo_info`) need to not be defined in other repo files |

## Tags

| Name | Meaning |
|:---|:---|
| add-repo | adding the pg repo to the `repos_file_name` file and enabling it |
| inst-rpms | installing the rpms |
| postgis | installing the postgis rpms |

## Examples 

This will enable postgres repo and install the postgres and python rpms 
```yaml
- import_role:
    name: inst-rpms
```
---

This will enable postgres repo and install the postgres, postgis and python rpms
```yaml
- import_role:
    name: inst-rpms
  vars:
    postgis: yes
```

## Diffrent Network

When moving to a diffrent network you should check if the following parameters are fitting to your environment:
- `pg_repo_info`
- `postgis_repos`
- `python_postgres_repos`

