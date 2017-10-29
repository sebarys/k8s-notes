# Resource quotas

## Quotas

When your cluster is using by many people resource management becomes more important.

Cluster can be divide in namespaces and enable resource quotas on it. This can be done by ResourceQuota and ObjectQuota objects.

Each container can specify `request capacity` - minimum amount of resources the pod needs and `resource limits` - container will not use more than that number of resources.

Administrator can set following resource limits within a namespace:
- requests.cpu - sum of CPU requests of all pods
- requests.mem - sum of memory requests of all pods
- requests.storage - sum of storage requests of all persistent volumes
- limits.cpu - sum of cpu limits of all pods
- limits.memory - sum of memory limits of all pods

Administrator can set following objects limit:
- configmaps
- persistentvolumeclaims
- pods
- replicationcontrollers
- resourcequotas
- services
- services.loadbalancer
- services.nodeports
- secrets

## Namespaces

Namespaces allow you to create virtual clusters within the same physical cluster. Standard namespace is 'default', kubernetes specific resources have own namespace 'kube-system'.

`kubectl create namespace MY_NAMESPACE` to create new namespace

To set given namespace as default
```
$ export CONTEXT=$(kubectl config view | awk '/current-context/ {print $2}')
$ kubectl config set-context $CONTEXT --namespace=MY_NAMESPACE
```
