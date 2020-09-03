# Ansible Docker setup

[![Build Status](https://travis-ci.com/LolloneS/ansible-experiment.svg?branch=master)](https://travis-ci.com/LolloneS/ansible-experiment)

## What?
This repository automatically configures 2 (but you can modify it to configure *n*) CentOS VMs and does the following:
* expand the VMs' disks and filesystems to have enough free space (defined as 40GB)
* install Docker, docker-compose on the VMs
* install Docker swarm and set up a node as the master, the others as slaves
* safely expose the Docker daemon APIs via TLS and provide the user with the keys and CA certificate needed for secure connection

Want to try it? Just run `vagrant up && ansible-playbook -i hosts main.yml` and enjoy!


## Prerequisites

> Beware that all of the following relies on Python 3. In other words, the Ansible version that's being used is the Python3 version, `pip` is an alias for `pip3`, etcetera. Python 2 is *not* being used.

* Ansible: [installation guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
* Vagrant: [download](https://www.vagrantup.com/downloads.html)
* vagrant-disksize: `vagrant plugin install vagrant-disksize`
    * we use this plugin to resize partitions in the VMs
* vagrant-hostsupdater: `vagrant plugin install vagrant-hostsupdater`
    * automatically add (remove) each new (deleted) VM's hostname to (from) the `/etc/hosts` file 
* ansible-lint: `pip install ansible-lint`

Additional tools:
* [yamllint](https://github.com/adrienverge/yamllint): used for linting the YAML files in the repository

## Information of interest
### Directory layout
The directory layout follows the best practices as per the [documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html).

### Local testing
In order to test the code locally, I rely on Vagrant to spin up two CentOS VMs as per the specification, basically copying what can be found [here](https://www.hamvocke.com/blog/local-ansible-testing/). In particular, `ansible.cfg` and `Vagrantfile` are both heavily inspired from the blog post.

### Docker setup
Docker is set up entirely using Ansible. The `docker` role is an adaptation of [this](https://github.com/geerlingguy/ansible-role-docker) work.

### Running the playbook
1. Create the Virtual Machines according to the Vagrantfile by running `vagrant up`
2. Run the playbook with `ansible-playbook -i hosts main.yml`

### Safely connecting to Docker
The connection to the remote Docker host is secure by default. This requires the client to have a certificate and a key that are recognized and validated by the remote Docker host. The [docker-tls.yml](roles/docker/tasks/docker-tls.yml) file copies these files to the local `certs_folder` and `keys_folder` directories defined in the `docker` role's `defaults`. When connecting to the remote Docker host, pass the copied file as follows:
```docker --tlsverify --tlscacert=/path/to/ca-ansible.crt --tlscert=/path/to/client-cert.crt --tlskey=/path/to/client-key.pem -H=REMOTE_HOST:2376 version```
where `REMOTE_HOST` is, in this case, either vm1.myapp.dev or vm2.myapp.dev. Notice that these must match the Common Names used when creating the certificates in [docker-tls.yml](roles/docker/tasks/docker-tls.yml), as per the [documentation](https://docs.docker.com/engine/security/https/).

## TODO
* Parametrize the copy of certs and keys from [docker-tls.yml](roles/docker/tasks/docker-tls.yml) on a per-host basis
* Improve the way Docker Swarm is deployed
* Test tasks with Molecule
* Remove conditional `{{ manager_ip }}` in Docker swarm setup
* Change VM disk size if empty space is not 40GB