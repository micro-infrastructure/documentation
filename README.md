# Documentation

The LOBCDER micro-infrastructure is setup of many depending parts. Here we describe how each interact and how to make use of them. There are two main scenarios, 1: setting up a demo setup using local VMs (mini-lobcder) or setting up a full installation.  First we give an overview of the architecture.

## Architecture
some refs to deliverables....

## Getting started mini-lobcder
Mini-lobcder is LOBCDER micro-infrastructure packaged inside a VM which can be used to interact locally. This is done using Vagrant, VirtualBox, and Ansible. In the setup a Kubernetes 1 node cluster is setup and demo account is setup. 
### Installation
- make sure you Vagrant and VirtualBox are installed on your system
- clone the repository  [mini-lobcder](https://github.com/micro-infrastructure/mini-lobcder)
- copy k8s-setup/user-infra.json.template to user-infra.json
- edit file...
- run make build
- this will take some time
- when ready a webdav point is created at 192.168.50.10:31002

....
 



