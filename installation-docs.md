# Installation Documentation
## Setting up a k8s cluster
Services in LOBCDER are pods that run on a K8s cluster. For this reason a K8s cluster is essential for getting the core services and user services running. Any K8s installation will suffice for LOBCDER. Once the K8s is installed you should have a kube config file which you can use with your local kubectl command.
## Setting up a Data Node
A Data Node (DN) is a k8s worker node which is designated for data processing and storage. The only difference from a standard K8s node is that specific local folders are reserved for LOBCDER users so that their containers (e.g Jupyter) can mount the local folder and access the data directly. So setting up a DN, at this stage, is the same as setting up a K8s worker node. It is ideal that DNs have public network interfaces so users can access the DN directly instead of going through the K8s master node.

## Setting up core services
The core services run in their own namespace: process-core. To create a namespace
```
kubectl create ns process-core
```
### Secret file
The core services share credentials. These are stored in a K8s secret file which needs to be generated. Mini-LOBCDER repo contains a script, [genVault.sh](https://github.com/micro-infrastructure/mini-lobcder/blob/master/k8s-setup/genVault.sh), for generating a secret file which can also be used to generate the file for a full installation. The secret file containes database passwords used by core-mongo and keys used by core-infra. Once the secret file is created(default name is vault.yml) it can be deployed: 
```
kubectl create -f vault.yml
```

### Setting up core-mongo
User information is held in a mongo database this need to be setup before running core-infra service. Essentially core-mongo is a default mongo database. A minimum pod file is provided [here](https://github.com/micro-infrastructure/core-mongo/blob/master/core-mongo-no-pv-mini.yaml). The pod sets env variables from the secret file and creates a *process* database and a default *core-infra* user. Please note that this pod is **not** using a persistent database. The yaml file can be adjusted to mount a local folder on one of the K8s nodes and use that as the data base. An example setup of this could be found [here](https://github.com/micro-infrastructure/core-mongo/blob/master/core-mongo-no-pv.yaml) the */mnt/mongo/db* local folder is mounted to */data/db* mongo container folder. Obviously, the local folder needs to change to your local folder. Also a nodeSelector needs to be set:
```
nodeSelector:
       kubernetes.io/hostname: HOSTNAME
```
Where HOSTNAME is the K8s node where the local folder is used to mount into the mongo container. This forces K8s to only deploy core-mongo on a designated node. Deploying core mongo involves simply deploying the yaml file E.g.
```
kubectl create -f core-mongo-no-pv-mini.yaml
```

### Setting up core-infra
Core-infra is the main service that admins and users interact with to create micro-infrastructures. The yaml file for core-infra is found [here](https://github.com/micro-infrastructure/core-infra/blob/master/core-infra.yaml). First you need to label one of the nodes as *core*. This is used to only submit core services to a designated node.
```
kubectl label node HOSTNAME type=core
```
Where HOSTNAME is a K8s node. For small installations HOSTNAME can be the same as the node used for core-mongo. After this deploying core-infra can be done
```
kubectl create -f core-infra.yaml
```

To check that everything is running you can check with kubectl 
```
kubectl get pods -n process-core
```

which should return something like
```
NAME                                     READY   STATUS    RESTARTS   AGE
core-infra-deployment-544b7f4669-xhj6d   2/2     Running   0          50d
core-mongo                               1/1     Running   0          97d
```

To start interacting with core-infra you need the admin token which is saved in /app/adminToken file on the core-infra container. You can pull this file using
```
 kubectl cp -c web -n process-core CORE_INFRA_NAME:/app/adminToken adminToken
```

Where CORE_INFRA_NAME is the pod name retrieved from the previous step. The token is added as a header, **x-access-token** in API communication to core-infra e.g. creating a user

```
curl --location --request PUT '$K8S_MASTER:30000/api/v1/user' \
--header 'x-access-token: $ADMIN_TOKEN' \
--header 'Content-Type: text/plain' \
--data-raw '{
	"email": "user@example.com",
	"namespace": "user",
	"folders": [],
	"staticPorts":	{}
}'
``` 

Where K8S_MASTER is the ip/hostname of the master node and ADMIN_TOKEN is the admin token retrieved in the previous step. This creates a user named user and the response is a user token for interacting with core-infra. The core-infra API reference is found [here]()(https://github.com/micro-infrastructure/core-infra/blob/master/API.md)
