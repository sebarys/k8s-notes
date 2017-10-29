# Create Pod from mainfest

To create pod from manifest you can execute `kubectl create -f {pathToManifest}`. Example manifest is in *deployments* directory.

After create pods from pod-helloworld manifest we can check if Pod is working properly by forward port 3000 in Pod to our  localhost 8081 port: <br>
`kubectl port-forward nodehelloworld.example.com 8081:3000`

Now we can call our container and verify response `curl http://localhost:8081/`

Other method to see how is working our Pod is exposing it in a service.
`kubectl expose pod nodehelloworld.example.com --type=NodePort --name nodehelloworld-service`

By `kubectl get services` you can get service port number, save it and open in AWS security group.

After opening port in security group you can curl your service externally `curl http://api.kubernetes.{ourDomain}:{portNumber}/`

In AWS we have possibility to use LoadBalancer which will be route the traffic to the correct Pod. There are also other solutions like haproxy/nginx or directly exposing ports, but using LoadBalancer is the most practical.
Example deployment YML for LoadBalancer service is in *deployments/helloworld-service.yml*. </br>
*type: LoadBalancer* in deployment on AWS will start automatically ELB.

To create service from manifest: `kubectl create -f deployments/helloworld-service.yml`

LoadBalancer for created service is available in EC2 -> Load Balancers. </br>
ELB have it own DNS name which we can add to our Hosted zone in Route 53. In our hosted zone we can simply add new Record Set by clicking button 'Create Record Set', add name and choose it as alias for our LoadBalancer. </br>
After saving new record set you can directly call your app by configured alias name.
