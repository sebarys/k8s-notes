# Ingress
Ingress is a solution that allows inbound connection to the cluster. It is an alternative to the external LoadBalancer or nodePorts. External load balance like in e.g. cost money so you could use ingress. Additionally you could better customise your traffic distribution.

<p align="left"><img src="img/ingress.png"/></p>

In ingress you can define your own custom rules. Example of ingress definition you can find in file *deployments/ingress/ingress.yml*

Additionally we create ReplicationController from manifest in  *deployments/ingress/nginx-ingress-controller.yml*

In directory *deployments/ingress* we have definition of three services: helloworld-v1, helloworld-v2 and echoheaders. Our ingress controller will distribute traffic to helloworld-v1 and helloworld-v2. As default service if path will not match will be echoheaders service.

To run example:
- `kubectl create -f ingress.yml`
- `kubectl create -f nginx-ingress-controller.yml`
- `kubectl create -f echoservice.yml`
- `kubectl create -f helloworld-v1.yml`
- `kubectl create -f helloworld-v2.yml`

By executing `minikube ip` we can get ip of our cluster. When you try to curl on this IP address you will receive response from echoheaders service which is set as default backend service in nginx-ingress-controller. When we want to call on of our helloworld service locally we can use curl with specified Host header:
`curl <ip_address> -H 'Host: helloworld-v1.example.com'`
