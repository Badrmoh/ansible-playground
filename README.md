# What is ansible?

Ansible is a configuration management tool. Hence it can be used to manage configurations on both remote and local hosts, though it is usually used for remote hosts.

### Examples

1. Restart all hosts at once or one by one
2. Install certain packages on all hosts
3. Check if certain files exist on the remote host then if found execute certain tasks
4. Render templates and copy them on remote hosts then restart the app

# Directory Structure

Directory structure has many standards but can be customized for each case, so this structure can be different.

There are two main directories in this repository:


**inventories**: here hosts and variables are defined
```
├── development
│   ├── group_vars
│   │   ├── all.yml
│   │   ├── env.secret.yml
│   │   └── env.yml
│   └── hosts.yml
├── production
│   ├── group_vars
│   │   ├── all.yml
│   │   ├── env.secret.yml
│   │   └── env.yml
│   └── hosts.yml
└── staging
    ├── group_vars
    │   ├── all.yml
    │   ├── env.secret.yml
    │   └── env.yml
    └── hosts.yml
```

**playbooks**: here ansible scripts (playbooks) are defined
```
├── copy_env_file.yml
├── list_hosts.yml
└── templates
    └── env.j2
```

## Inventories

An inventory is a collection of hosts. Under inventories directory, there can be multiple sub-directories, each should have certain structure:
```
├── group_vars
│   ├── all.yml
│   ├── env.secret.yml
│   └── env.yml
└── hosts.yml
```

### Hosts

This structure is used by ansible, though hosts.yml can be named differently or even further subdivided (e.g app.yml and db.yml).

hosts in ansible are simply a connection definition. In this definition some variables must be set, e.g. ansible_host and ansible_user.



### Groups

A group is a collection of hosts. Typically groups are used to set common variables for different hosts. Groups are also used in playbooks to refere to a collection of hosts instead of typing them indevidually
e.g. `app` instead of `app1 app2 app3 ..`

Defining a group is done in the hosts file.


### Variables


- variables in ansible are attached to hosts. There are three types of variables, predefined (automatically set by ansible), host vars and group vars. 


- Defining variables can be done in hosts yaml file, hosts_vars directory (not included in this example) and under group_vars directory.


- ansible identifies which files to use as variables source automatically by using defined groups in hosts yaml file. E.g. a group named `env` would make ansible use variables defined in a file under `group_vars/env.yml`


- files under group_vars can be also encrypted if they need to. Ansible automatically reads a variable named `ANSIBLE_VAULT_PASSWORD_FILE` where password is stored. Password can also be interactively provided using `--ask-vault-pass` which is not suitable for pipelines



## Playbooks

- here scripts / playbooks are defined

- playbooks consists of different tasks

- if a playbook consists of many tasks that are related, tasks can be grouped to separate files under `playbooks/tasks/`, then included in the playbook

- there are other features in ansible like roles which is a more holistic approach to grouping tasks.


## Templates

- templates are usually used in roles but for this example it is stored under `playbooks/templates/`

- templates are written in jinja2



# Guide

1. Create a password file

```export ANSIBLE_VAULT_PASSWORD_FILE=$HOME/.vault-pass
echo 'password' > $ANSIBLE_VAULT_PASSWORD_FILE`
```

2. Encrypt secrets files. This will automatically read ANSIBLE_VAULT_PASSWORD_FILE

`ansible-vault encrypt inventories/development/group_vars/env.secret.yml`


3. Check encrypted file

```cat inventories/development/hosts.yml
ansible-vault view inventories/development/group_vars/env.secret.yml
```

4. Run playbook that renders dotenv template

`ansible-playbook -i inventories/development/hosts.yml playbooks/copy_env_file.yml`

