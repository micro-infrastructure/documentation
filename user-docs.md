# User Documentation
As described in the architecture. User's interaction with the system is two-fold; first, you create a description file for your micro-infrastructure this usually includes a set of sshfs adaptors to remote servers and a set of services that work on the data such as Jupyter, scp2scp or any other container developed by the user. Secondly, after the description file has been submitted to core-infra service, you interact with your own new services. The following steps will give you more details on how to deploy a micro-infrastructure:
- getting an access token to access core-infra: 
Before being able to access the core-infra service and able to submit micro-infrastrucure you need an access token to access the API. This can be done be asking the admin of the system to create one. Alternativaly you can experiment with a local [mini-lobcder](https://github.com/micro-infrastructure/mini-lobcder/blob/master/README.md) installation which creates a default user on installation. 
- create a micro-infrastructure description file follow instructions [here](https://github.com/micro-infrastructure/mini-lobcder/blob/master/Micro-Infra.md)
- create a custom container. Core-infra has some pre-configured containers but you can create you own container images to be used in the micro-infrastructure for this follow instructions [here](https://github.com/micro-infrastructure/mini-lobcder/blob/master/Custom-Container.md) 
- query core-infra service for runnning services. Once deployed, the exposed services will get a port > 30000 so they can be accessed from outside the k8s cluster. These ports are dynamically assigned and need to be queried after the micor-infrastrucure is deployed. This can be done using curl:
```
curl --location --request GET 'HOST:30000/api/v1/infrastructure' \
--header 'x-access-token: ACCESS_TOKEN' \
--data-raw ''
```
where HOST is the hostname of the k8s cluster, in case mini-lobcder this would be 192.168.50.10 and ACCESS_TOKEN is the token provided by the k8s admin. In case of mini-lobcder, the token can be found in the file k8s-setup/userInfraToken. The curl command returns a json formatted response with details about your running containers. More information checkout the [api documentation](https://github.com/micro-infrastructure/core-infra/blob/master/API.md).
- Access your services. Using the ports from the previous step you can access your services. For mini-lobcder you can access the webdav service using http://192.168.50.10:PORT from your browser, Similarly the Jupyter notebook can be accessed using http://192.168.50.10:PORT.
