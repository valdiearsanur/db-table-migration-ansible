# Ansible Roles for database migration
This is ansible Roles for moving table data (by custom SQL) between two different databases.

# Table of Contents  
1. [Prerequisites](#1-prerequisites)  
2. [Test Drive Ansible Roles](#2-test-drive-ansible-roles)
3. [Execute Ansible Roles](#3-execute-ansible-roles)
4. [Variable Definition](##4-variable-definition)

## 1. Prerequisites
You only need Ansible installed (above python) to use this : `pip install ansible`. But if you want to perform isolated test, you can follow steps below : 

1. Docker engine
1. Python dependencies: Docker SDK for python, Ansible, Ansible Molecule
    ```
    pip install -r requirements.txt
    ```

## 2. Test Drive Ansible Roles
We use Molecule in the development and testing phase of Ansible roles. Molecule provides support for testing with multiple instances, operating systems and distributions, virtualization providers, test frameworks and testing scenarios.
This repo has been set up with molecule located in `roles/{role-name}/molecule`. There are some options to testing Ansible Roles using Molecule:

### 2.1. Deploy into docker instance only
We can then tell Molecule to test our role against our instance with:
```
molecule converge
```
If we want to manually inspect the instance afterwards, we can run:
```
molecule login
```
Finally, we can exit the instance and destroy it with:
```
molecule destroy
```

### 2.2. Run full test sequence
Molecule provides commands for automatically managing the [Scenario Sequence](https://molecule.readthedocs.io/en/stable/configuration.html#root-scenario), using command below:
```
molecule test
```

## 3. Execute Ansible Roles


### 3.1. Clone in Project
Clone this to target project. You can use command below and change VERSION_NUMBER with desired version (see list version in https://github.com/valdiearsanur/db-table-migration-ansible/-/tags)

```
git clone https://github.com/valdiearsanur/db-table-migration-ansible.git
```


### 3.2. Create ansible inventory
from `./ansible` directory, create a `.ini` file with name represent the environment, for example `staging.ini`. The file will contains SSH connection and variables as shown in code below. You can find variable definition in next section.
```
[inventory]
backup ...
restore ...

[inventory:vars]
<VARIABLES>
```

For example :

**[project_directory]/ansible/staging.ini**
```
[inventory]
backup ansible_connection=local
restore ansible_connection=local

[inventory:vars]
database_backup_name=db_source
database_backup_sql=SELECT id, field as field_v2 from some_table

database_restore_name=db_target
database_restore_table_name=some_table_v2
database_restore_table_column_order=id, field_v2
```

### 3.3. Run `ansible-playbook`
from your project directory (if you are in `./ansible`, please change your working directory with `cd ..`), run command below. Please change INVENTORY_INI_FILE with `.ini` file you create previously.

```
ansible-playbook -i <INVENTORY_INI_FILE> db-table-migration.yml
```

Example commands :
```
ansible-playbook -i ansible/staging.ini db-table-migration.yml"
```

## 4. Variable Definition
### database_backup_name
**default ``**

Backup database name.

---

### database_backup_sql 
**default `SELECT *`**

SQL represent data that need to be backed up.

---

### database_restore_name 
**default ``**

Target (restore) database name.

---

### database_restore_table_name 
**default ``**

Table name in target (restore) database.

---

### database_restore_table_column_order 
**default ``**

Column order when restoring database. It should be same order as the SELECT statement (target CSV order).

---

