awx_population
=========

This gnarly role will create Organizations, Projects, Credentials, Templates, and Schedule for automated runs.

TODO: Configure with sample project so we can do end to end testing via travis.ci

Requirements
------------

This role will require rights to ssh into the awx host.  Then it will use you ssh credentials to also attempt the configuration build out

Role Variables
--------------

awx_organizations - List of Organizations you want to create, this is a requirement for further object creation

```
  [
    "name": "FOO INC"
  ]
```

awx_creds - List of objects to build out machine, scm, and vault credentials (I suggest vaulting this)
```
  [
    {
      "name": "FOO BAR Root User",
      "organization": "FOO INC",
      "kind": "ssh",
      "user": "root",
      "password": "chickenwing"
    }
  ]
```

awx_projects - List of objects for building projects
```
  [
    {
      "name": "FOOBAR",
      "organization: "FOO INC",
      "scm_update_on_launch": False,
      "scm_update_cache_timeout": 120,
      "scm_type": "git",
      "scm_url": "git@bitbucket.org:fooinc/radproject.git",
      "scm_credential":
      "SRV ENG Bitbucket",
      "scm_branch":
      "master"
    }
  ]
```

awx_inventory - ONE object to create the inventory based on the inventory file you point to
```
  "name": "SRV ENG Inventory", "organization": "FOO INC"
```

awx_templates - List of objects to assist with creating templates.  Listed credentials must be included

```
  [
    { "name": "Test Template",
      "inventory": "SRV ENG Inventory",
      "project": "FOOBAR",
      "playbook": "playbooks/do_some_rad_stuff.yml",
      "credentials": [
        "FOO BAR Root User",
        "FOO BAR Vault"
      ]
    }
  ]
```

Dependencies
------------

Group for awx host, named awx.  See example play below

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```    
- name: Gather localhost facts - used later
  hosts: localhost
  connection: local

  tasks:
    - name: Install pip packages
      pip:
        name:
          - ansible_tower_cli
        state: present
      delegate_to: localhost
      run_once: True

- name: Create Credentials, Projects, Templates, Schedules
  hosts: awx
  remote_user: centos # required because some tower modules auth from a local user
  gather_facts: False

  roles:
    - awx_population
```

Example commands
----------------
```
ansible-playbook -i hosts.yml playbooks/awx_project_deployments.yml --ask-vault-pass --ask-pass
```

```
ansible-playbook -i hosts.yml playbooks/awx_project_deployments.yml --ask-vault-pass -e ansible_ssh_pass=$super_secret_pw
```

License
-------

BSD

Author Information
------------------

Riley Schuit (riley.schuit@gmail.com)
