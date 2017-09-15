# Services in Kubernetes

Service is a bridge between mortal Pods and other services. Service is create when use `kubectl expose` command.
Creating service will expose endpoint for Pods and depends on type:
- ClusterIP - it will be virtual and visible only in cluster (default)
- NodePort - port that is the same on each node so it can be visible externally
- LoadBalancer - a load balancer created by a cloud provider that will route external traffic to every node on the NodePort

Example manifest for service is in *deployments/helloworld-service.yml*. By default services are running on ports in port range 30000-32767. It could be changed by setting flag *--service-node-port-range* in kube-apiserver script.

To create service: `kubectl create -f deployments/helloworld-service.yml` </br>
then we can get information about service by executing `kubectl describe service helloworld-service`
