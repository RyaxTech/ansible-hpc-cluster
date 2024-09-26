
# Intro

Commands are showed as issued from the current directory.
Global configurations are on `ansible.cfg`. To start
edit `inventory/hosts.yaml` to reflect your infrastructure.
The user on the inventory must have sudo (without password)
access for most of the commands to work. The commands are 
issued relatively  to working directory ansible, `cd ansible`
before starting.

## Configuration

* The configuration expects to have Ubuntu OS and ubuntu user created
* create private key pairs for the user ubuntu to launch the playbooks (the user needs to have sudo rights)
* insert your public keys into `playbooks/template/authorized_keys`
* change the IPs and hostnames in `inventory/hosts.yaml` to reflect your cluster
* change the slurm node names hardware characteristics in `playbooks/templates/slurm.conf.j2` to reflect your cluster
* you may change the slurm version in playbook/slurm.yaml
* Slurmdbd is configured but not started automatically
* Follow detailed instructions below regarding the playbooks you need to install. The first 3 playbooks are mandatory for slurm to function correctly. The last 2 playbooks are optional.

## Install NFS

Install NFS.

```sh
 ansible-playbook ./playbooks/nfs.yaml
```

## Create new user

Create a new user, will create username with same userid accross all
nfsclient nodes, then it creates the user home only on the nfs server.

```sh
ansible-playbook --extra-vars "username=ryax userid=1044" ./playbooks/add-user.yaml
```

# Install slurm

```sh
ansible-playbook ./playbooks/slurm.yaml
```

## Finalize slurmdbd configuration

Add the slurm user in the DB and grant all permissions on the slurm DB using the following commands.

```sh
sudo mysql
MariaDB [(none)]>create user 'slurm'@'localhost';
MariaDB [(none)]>grant all on slurm_acct_db.* TO 'slurm'@'localhost';
```

Uncomment the following lines in your slurm.conf file. Copy the slurm.conf file on all compute nodes. 

```sh
AccountingStorageHost=localhost
AccountingStoragePort=6819
AccountingStorageType=accounting_storage/slurmdbd
```

Copy the slurm.conf file on all compute nodes. Restart the slurmd on all nodes. Start slurmdbd and restart slurmctld.


# Install singularity

```sh
ansible-playbook ./playbooks/singularity.yaml
```

# Install openmpi

```sh
ansible-playbook ./playbooks/openmpi.yaml
```
