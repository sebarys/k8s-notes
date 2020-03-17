# Deployments in Kubernetes

## ReplicationSet

ReplocationSet is the next generation of ReplicationController. It supports selectors based on filtering according a set of values ('environment' either 'dev' or 'qa')

# Deployment object

Deployment object is a declaration that allows to do app deployments or updates. In deployment you describe a desired state of your application, later Kubernetes make sure that cluster is matching this state.

With deployment object you can:
- create a deployment
- update a deployment
- do rolling updates
- rollback to previous version

Example deployment object is in *deployments/helloworld-deployment.yml*

After creating deployment we can expose it e.g. as nodePort </br> `kubectl expose deployment helloworld-deployment --type=NodePort` </br>
Now we can check exposed port of service, and in minikube to get url to service execute: `minikube service helloworld-deployment --url`

To update version of deployment containers: </br>
`kubectl set image deployment/helloworld-deployment k8s-demo=wardviaene/k8s-demo:2`

To see if update complete successfully we can execute: </br>
`kubectl rollout status deployment/helloworld-deployment`

History of updates is available at: </br>
`kubectl rollout History deployment/helloworld-deployment`

To undo last updates: `kubectl rollout undo deployment/helloworld-deployment`

By default Kubernetes stores 2 previous revision version. To increase it we can set in deployment spec *revisionHistoryLimit* parameter.
