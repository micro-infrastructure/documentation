# Documentation

The LOBCDER micro-infrastructure is setup of many depending parts. Here we describe how each interact and how to make use of them. There are two main scenarios, 1: setting up a demo setup using local VMs (mini-lobcder) or setting up a full installation.  First we give an overview of the architecture.


## Getting started mini-lobcder
Mini-lobcder is LOBCDER micro-infrastructure packaged inside a VM which can be used to interact locally. This is done using Vagrant, VirtualBox, and Ansible. In the setup a Kubernetes 1 node cluster is setup and demo account is setup. 
### Installation
Visit [mini-lobcder](https://github.com/micro-infrastructure/mini-lobcder/blob/master/README.md) for instructions.

## LOBCDER-core-infra REST API
Visit [lobcder-core-infra](https://github.com/micro-infrastructure/core-infra/blob/master/API.md) for instructions.

## LOBCDER-copy REST API
Visit [lobcder-copy](https://github.com/micro-infrastructure/service-scp2scp/blob/master/API.md) for instructions.
 
## LOBCDER LOFAR adaptors
There are two LOFAR adaptors that can be used to move data from the LOFAR LTA to an HPC cluster. Visit their respective repositories for instructions:

- [adaptor-lofar-stage](https://github.com/micro-infrastructure/adaptor-lofar-stage)
- [adaptor-lofar-download](https://github.com/micro-infrastructure/adaptor-lofar-download)

To deploy these services as part of mini-lobcder, see the [lofar template](https://github.com/micro-infrastructure/mini-lobcder/blob/master/k8s-setup/user-infra.json.template-lofar).



