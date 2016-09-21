# TICK Stack Configuration

This repository contains configuration management source code which will deploy the latest [TICK stack](https://www.influxdata.com/get-started/what-is-the-tick-stack/).

Two tools are used to automate the process of TICK stack deployment and confguration, these are `Vagrant` and `Ansible`.

## Requirements

### Vagrant

Vagrant allows developers to easily spinup disposable environments for testing code during development.

In this case we use it for two reasons:

1. The creation of virtual machines for the deployment.
2. To manage Ansible runs.

Installation instructions can be found on the [Vagrant website](https://www.vagrantup.com/docs/installation/)

### Ansible

Ansible is a modern day configuration management and orchestration tool in which the configuration management code is considered to be self-documenting .

Ansible stores code in something known as a `playbook`. Playbooks contain all the tasks required to reach a certain outcome. Be it deploy some code, update packages on a server etc.

Please see the [Ansible installation guide](http://docs.ansible.com/ansible/intro_installation.html<Paste>)

## Getting started

The  tasks defiend in the `playbook.yml` can be thought of as not only a document describing on how to deploy the TICK stack, but will get the stack up and running for you automatically!

Before we get started, we need a virtual machine to run the Ansible playbooks against, to do that we use vagrant like so:

```
 $ vagrant up
```
This step make take some time as it will perform the following steps:

1. Download the vagrant machine image.
2. Boot the VM.
3. Run Ansible playbooks against the VM, installing the entire TICK stack.

## Going Further

### Working with Ansible and Vagrant

To further develop the playbooks, the `playbook.yml` can be altered, and then the changes can be easily re-applied by running:

`$ vagrant provision`

### Starting Fresh

To start with a fresh environment, we can do the following:

```
 $ vagrant destroy
 $ vagrant up
```
