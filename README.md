
# Intro

Commands are showed as issued from the current directory.
Global configurations are on `ansible.cfg`. To start
edit `inventory/hosts.yaml` to reflect your infrastructure.
The user on the inventory must have sudo (without password)
access for most of the commands to work. The commands are 
issued relatively  to working directory ansible, `cd ansible`
before starting.

## Configuration

* create private key pairs for the user to launch the playbooks (the user needs to have sudo rights)
* insert your public keys into `playbooks/template/authorized_keys`
* change the IPs and hostnames in `inventory/hosts.yaml` to reflect your cluster
* you may change the slurm version in playbook/slurm.yaml
* Follow detailed instructions below regarding the playbooks you need to install

## Install NFS

Install NFS.

```sh
 ansible-playbook --private-key=../ubuntu.pem playbooks/nfs.yaml
```

## Create new user

Create a new user, will create username with same userid accross all
nfsclient nodes, then it creates the user home only on the nfs server.

```sh
ansible-playbook --private-key=../ubuntu.pem --extra-vars "username=ryax userid=1044" ./playbooks/add-user.yaml
```

# Install slurm

```sh
ansible-playbook --private-key=../ubuntu.pem ./playbooks/slurm.yaml
```

# Install singularity

```sh
ansible-playbook --private-key=../ubuntu.pem ./playbooks/singularity.yaml
```

# Install openmpi

```sh
ansible-playbook --private-key=../ubuntu.pem ./playbooks/openmpi.yaml
```
