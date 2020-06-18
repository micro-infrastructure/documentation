# Documentation

The LOBCDER micro-infrastructure is setup of many depending parts. Here we describe how each interact and how to make use of them. There are two main scenarios, 1: setting up a demo setup using local VMs (mini-lobcder) or setting up a full installation.  First we give an overview of the architecture.

## Architecture
![](lobcder_Arch.png)

The above figure illustrates the interactions of the main LOBCDER components and repositories. All the components run as pods in a Kubernettes cluster. The core services, core-infra and core-mongo, run in a process-core namespace. The user pods which make up the user's micro-infrastructure run in seperate namespace per user. User interact with the system by first submit a [micro-infra deployment file](https://github.com/micro-infrastructure/core-infra/blob/master/MICRO-INFRA.md) to [core-infra](https://github.com/micro-infrastructure/core-infra) service. The core-infra service translates the the micro-infra file to k8s deployment and service yaml descriptions, creates namespaces, secrets, credentials and submits them to the k8s cluster. This creates a pod with all the containers requested by the user.

A standard deployment involves attaching to remote HPC file systems using ssh/sshfs, expose these filesystems to the user using a webdav endpoint and deploy the [service-scp2scp](https://github.com/micro-infrastructure/service-scp2scp) API which is used by [IEEE]()
to do 3rd party copies between HPC clusters. 

A special case deployment is wen the k8s node is a DTN (Data Transfer Node) then containers can mount a local host folder E.g. [adaptor-ssh](https://github.com/micro-infrastructure/adaptor-ssh) mounts a local folder and exposes it through ssh.  

## Getting started mini-lobcder
Mini-lobcder is LOBCDER micro-infrastructure packaged inside a VM which can be used to interact locally. This is done using Vagrant, VirtualBox, and Ansible. In the setup a Kubernetes 1 node cluster is setup and demo account is setup. 
### Installation
Visit [mini-lobcder](https://github.com/micro-infrastructure/mini-lobcder/blob/master/README.md) for instructions.

## For users
As described in the architecture above. User's interaction with the system is two-fold; first, you create a description file for your micro-infrastructure this usually includes a set of sshfs adaptors to remote servers and a set of services that work on the data such as Jupyter, scp2scp or any other container developed by the user. Secondly, after the description file has been submitted to core-infra service, you interact with your own new services. The following steps will give you more details on how to deploy a micro-infrastructure:
- getting an access token to access core-infra: 
Before being able to access the core-infra service and able to submit micro-infrastrucure you need an access token to access the API. This can be done be asking the admin of the system to create one. Alternativaly you can experiment with a local [mini-lobcder](https://github.com/micro-infrastructure/mini-lobcder/blob/master/README.md) installation which creates a default user on installation. 
- create a micro-infrastructure description file:
follow instructions [here](https://github.com/micro-infrastructure/mini-lobcder/blob/master/Micro-Infra.md)
- create a custom container:
follow instructions [here](https://github.com/micro-infrastructure/mini-lobcder/blob/master/Custom-Container.md) 
- query core-infra service for runnning services. Once deployed, the exposed services will get a port > 30000 so they can be accessed from outside the k8s cluster. These ports are dynamically assigned and need to be queried after the micor-infrastrucure is deployed. This can be done using curl:
```
curl --location --request GET 'HOST:30000/api/v1/infrastructure' \
--header 'x-access-token: ACCESS_TOKEN' \
--data-raw ''
```
where HOST is the hostname of the k8s cluster, in case mini-lobcder this would be 192.168.50.10 and ACCESS_TOKEN is the token provided by the k8s admin. In case of mini-lobcder, the token can be found in the file k8s-setup/userInfraToken. The curl command returns a json formatted response with details about your running containers. More information checkout the [api documentation](https://github.com/micro-infrastructure/core-infra/blob/master/API.md).
- Access your services. Using the ports from the previous step you can access your services. For mini-lobcder you can access the webdav service using http://192.168.50.10:PORT from your browser, Similarly the Jupyter notebook can be accessed using http://192.168.50.10:PORT.

## LOBCDER-core-infra REST API
Visit [lobcder-core-infra](https://github.com/micro-infrastructure/core-infra/blob/master/API.md) for instructions.

## LOBCDER-copy REST API
Visit [lobcder-copy](https://github.com/micro-infrastructure/service-scp2scp/blob/master/API.md) for instructions.
 
## LOBCDER LOFAR adaptors
There are two LOFAR adaptors that can be used to move data from the LOFAR LTA to an HPC cluster. Visit their respective repositories for instructions:

- [adaptor-lofar-stage](https://github.com/micro-infrastructure/adaptor-lofar-stage)
- [adaptor-lofar-download](https://github.com/micro-infrastructure/adaptor-lofar-download)

To deploy these services as part of mini-lobcder, see the [lofar template](https://github.com/micro-infrastructure/mini-lobcder/blob/master/k8s-setup/user-infra.json.template-lofar).



