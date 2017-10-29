# Kubernetes

<p align="left"><img src="img/kubernetes.jpg"/></p>

**Controller Manager** (in master node) - never ending process which is responsible for valid state of cluster (supervisor with state managing)

**Replication Controller** - take care of maintain correct number of Pods

**Scheduler** - communication with kubelets

**Pod** - wrapper for one container or more containers (application)

**Service** - Pod is running internally, there is no way to call it external, Service is responsible for connecting between world and our Pods.ų

All creates Pods are defined in SDN network and are not visible from our network (there is a isolation), thanks to that we can create namespaces which will be fully isolated.

## Kubectl with Minikube (ver. 1.5)
Minikube runs a single-node Kubernetes cluster locally inside your laptop.

**Each thing could be done by commands, or if we run proxy we can use REST api.**
### Create a cluster
To install on macOS: `brew cask install minikube`
Kubectl config is in `cd ~/.kube/`

To run: `minikube start` - will automatically create and set kubectl context called "minikube"

`kubectl cluster-info` give an info about cluster status etc. </br>
`kubectl get nodes` show all nodes </br>

### Deploy an app
`kubectl run {name} --image={image} --port={port}`
create deployment, kubectl will:
- search for suitable node where instance could be run
- schedule the application to be run on that node
- configure cluster to reschedule the instance on a new node when needed

`kubectl get deployments` list deployments

By default deployed applications are visible only inside Kubernetes cluster. To view the application output without exposing it externaly, we can create a route. </br>
`kubectl proxy` create route between our host and the cluster (it must be working all time, like ssh connection to jumpbox)

`export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')` to export pod name

To get output of our application we can run a request
`curl http://localhost:8001/api/v1/namespaces/{namespace}/pods/$POD_NAME`

**Pod** is abstraction that represent a group of one or more application containers and some shared resources for them (storage as Volumes, unique cluster IP as Networking, info how run each container ports, img versions...). Apps in pods are always co-located and co-scheduled, also run in shared context on the same node. Pods are the atomic unit of kubernetes platform.

**Node** is a worker machine in kubernetes cluster (may be virtual or physical). Each node is managed by the Master.</br>
Every Node runs at least:
- `kubelet` - process responsible for communication between Master and Node, it manages the Pods and containers
- `container runtime` - responsible for pulling container image from a registry, unpacking and running the apps

`kubectl get pods` list all available pods </br>
`kubectl describe pods` return details about pods </br>
`kubectl logs $POD_NAME` list logs from pod </br>
`kubectl exec $POD_NAME {command}` execute command directly in container
`kubectl exec -it $POD_NAME bash` start bash session in container

### Expose app by creating Service
When worker node dies, Pods are lost and ReplicationController might dynamically drive the cluster back to desired state. A **Service** is an abstraction which defines a logical set of Pods and a policy by which to access them. Service is defined using YAML or JSON (like all kubernetes objects).

Each Pod has unique IP not exposed outisde the cluster without a Service. Services allow to receive traffic, by exposing in different way specyfing by **type** in ServiceSpec:
- **ClusterIP (default)** - exposes service on an internal IP in the cluster, service is reachable only inside the cluster
- **NodePort** - expose service on Node port using NAT, service is accessible from outside in given port (superset of ClusterIP)
- **LoadBalancer** - create external load balancer in the current cloud if supported and assign fixed IP to the service (superset of NodePort)
- **ExtenalName** - exposes the service using arbitrary name (specified by externalName in the spec), no proxy is used and kube-dns v.1.7 is required

<p align="left"><img src="img/services.svg" class="img-click-modal"/></p>

Services match a set of Pods using labels and selectors.

`kubect get services` list all services </br>
`kubectl expose deployment/{deploymentName} --type="NodePort" --port 8080` expose port with NodePort type as parameter

Now we have exposed service with unique cluser-IP and internal port and external-IP (IP of the node). </br>
`export NODE_PORT=(kubectl get services/first-app -o go-template='{{(index .spec.ports 0).nodePort}}')` to export value of node port
`kubectl describe nodes {nodeName}` return details of node </br>
`curl {describeNode.Addresses.InternalIP}:$NODE_PORT` to call our service </br>

**It doesn't care which node IP we will provide, proxy is global and we will receive the same on the same port in each node.**

Note: in minikube you can also generate service url by executing command `minikube service {serviceName} --url`

The deployment created automatically a label for our pod, you can see the name of the label by execute: `kubectl describe deployment`

`kubectl get pods/services -l {labelValue}` By using *-l* parameter we can refer to our services, pods etc.

`export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')` to get pod name and store it name in variable

`kubectl label pod $POD_NAME {newLabel}` to assign new label to our pod, {newLabel} need be in format {key}={value} (app=v1)

`kubectl delete service {serviceName}/ -l {label}` execute to delete service, note that it only delete service and pods are still available, you can check it by executing `kubectl exec -it $POD_NAME curl localhost:8080`

### Scale your app

Scaling will create/remove new Pods. Services have an integrated load-balancer that will distribute network traffic to all Pods of an exposed Deployment.

`kubectl scale deployments/{deploymentName} --replicas={desiredNumberOfReplicas}`command to scale deployment up

When you list pods by execute `kubectl get pods -o wide` you will list all Pods. Changes also are visible in deployment, see: `kubectl describe deployments/{deploymentName}`

Our service now will load balance traffic to all instances, to see it we can curl our service by nodeIp <br>`curl {describeNode.Addresses.InternalIP}:$NODE_PORT` and check that each request will be executed in different Pod.

To scale down service we simply need to set number of replicas to lower value that is actually.

### Update app

We can use **rolling update** to update our Pods incrementally to avoid downtime, by default number of unavailable Pods during update is one (this option could be configured by number of Pods or percentages). In kubernetes updates are versioned and we can easily revert to previous stable version.

To check current version on image in Pods execute `kubectl describe pods` (in Containers.{name}.Image)

To update image version use </br> `kubect set image deployments/{deploymentName} {deploymentName}={imageName}:{version}`
To check update effect we can curl our service and verify app version.

Now to try rollback version update try to update your application to non-existing version. After executing update you can see by `kubectl get deployments` that first Pod was tried to update and now you have n-1 available instances. <br> `kubectl get pods` will show that one instance have ErrImagePull status.

To rollback changes execute `kubectl rollout undo deployments/{deploymentName}`


To remove deployment: `kubectl delete deployments/{deploymentName}`

### Using Replication Controller
(https://github.com/Demonsthere/kubernetes-workshop/tree/master/01-kubernetes-basics/application)

We can use ReplicationController to create our pods and take care of them. Normally if you create app *kind: Pod* on fail pods will not be restarted. ReplicationController is ensure that expected number of Pods will be running all time.

json:

`curl POST {masterOrProxyUrl}/api/v1/namespaces/{namespace}/replicationcontrollers`

```
{
	"apiVersion": "v1",
	"kind": "ReplicationController",
	"metadata": {
		"name": "node-js"
	},
	"spec": {
		"replicas": 3, //to start this replicas we will use template below
		"selector": {
			"name": "node-js" //we select label
		},
		"template": {
			"metadata": {
				"labels": {
					"name": "node-js" //we define label
				}
			},
			"spec": {
				"containers": [
					{
						"name": "node-js",
						"image": "jonbaier/node-express-info:latest",
						"ports": [
							{
								"containerPort": 80
							}
						]
					}
				]
			}
		}
	}
}
```

Example manifest to run container in 2 replicas is in *deployments/helloworld-controller.yml* file.

`kubectl get rc` - list all running replication controllers </br>
`kubectl scale --replicas=1 rc/{ourRcName}` - scale given rc </br>
`kubectl rm rc/{ourRcName}` - delete ReplicationController, please note that this operation will terminate also all Pods belongs to this rc </br>
