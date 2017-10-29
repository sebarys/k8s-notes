# Service discovery

Kubernetes provide DNS service that can be used to find other services running on the same cluster.

Single pods don't need this service as the can contact each other directly by calling **localhost:podPort**. To make DNS work pod will always need a **ServiceDefinition**.

So, when we want to communicate between services we don't need to specify direct IP, service name is enough because using of DNS.

In *deployments/service-discovery/secrets.yml* are required to this example credentials. </br>
In *deployments/service-discovery/database.yml* is definition of mysql server Pod.

**kubectl create -f service-discovery/secrets.yml** will create credentials </br>
**kubectl create -f service-discovery/database.yml** will create Pod with mysql container.
Next we need to create database service by executing **kubectl create -f service-discovery/database-service.yml**

In **deployments/service-discovery/helloworld-db.yml** is deployment of node service which is our database aware. Pay attention to MYSQL_HOST env value, it is equal to database-service name. Now we can create helloworld-db deployment and helloworld-db-service service.

After deploying we can check logs one of helloworld-db pods, it could contains information about successfully connecting to database. To call service we can check service URL by executing **minikube service helloworld-db-service --url** and then curl received address.
