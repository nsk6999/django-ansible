# Ansible playbooks for Django with Postgres run on Gunicorn managed my supervisor, behind NGINX. Also launches EC2 instances quickly.

## Pre-requisites

1. SSH keys - The `pem` keys should be present in ~/.ssh/ for SSH authentication with EC2 instances.

2. The control server should have a public (id_rsa.pub) and private (id_rsa) RSA keys in `~/.ssh/`. (On Ubuntu this is usually generated by running `ssh-keygen`)
   *When the repository is cloned in the host system, these keys will be used to authenticate the host with Bitbucket.*

## Installation

* Clone this repo

* Install ansible system-wide

        sudo pip install ansible

* Install boto

    > Boto is a Python package that provides interfaces to Amazon Web Services.

        sudo pip install boto

* Add required variables in `env-vars`

    If you need to authenticate to an AWS account, add your access key id and secret access key to env-vars and source the file.

        source env-vars

---

## Roles

  The following roles are available for preparing a complete stack for a Django application with PostgreSQL database.

* Base - Install required packages
* Postgresql - Installs and configures Postgres to allow access to db user
* Git - Installs git, clones repository and checkout on specified branch
* Django - Creates virtual environment, install requirements, create gunicorn config, etc.
* Supervisor - Create supervisor script and start application
* NGINX - Create nginx configurations and start server

## Playbooks

### Playbook setup.yml

  This playbook will setup and deploy a project to a server. To run this playbook, use this command:

    ansible-playbook -i hosts setup.yml

1. **Role 'postgresql' currently authenticates user postgres with trust.**
2. Tested on EC2 servers with Ubuntu Server 14.04 AMI.

### Playbook status.yml

   This playbook checks the status of all servers

    ansible-playbook -i hosts status.yml


### Playbook launch_instance.yml

* Ensure that boto is installed before running this playbook.
* Provide the AWS account access key and secret in the env variables `AWS_ACCESS_KEY_ID` and `AWS_ACCESS_SECRET_KEY`.

  When both these conditions are satisfied, run this command:

    ansible-playbook -i hosts.local launch_instance.yml

  This book takes these variables: key_name, instance_type, security_group, image, region, iam_role_name. Each of these variables can be overridden on the command line with `--extra-vars`. This playbook will launch an instance for the AWS account, assigned to a role with name 'iam_role_name', instance type 'instance_type' and so on.