# ansible-challenge

## Prerequisites
* Ansible: [installation guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
* Vagrant: [download](https://www.vagrantup.com/downloads.html)
* vagrant-disksize: `vagrant plugin install vagrant-disksize`
    * we use this plugin to resize partitions in the VMs
* vagrant-hostsupdater: `vagrant plugin install vagrant-hostsupdater`
    * automatically add (remove) each new (deleted) VM's hostname to (from) the `/etc/hosts` file 


## Information of interest

### Directory layout
The directory layout follows the best practices as per the [documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html).

### Local testing
In order to test the code locally, I rely on Vagrant to spin up two CentOS VMs as per the specification, basically copying what can be found [here](https://www.hamvocke.com/blog/local-ansible-testing/). In particular, `ansible.cfg` and `Vagrantfile` are both heavily inspired from the blog post.
