# Config Map

Not secret parameters could be put in ConfigMap as key-value pairs. Our app can read them using:
- environment variables
- container commandline arguments in the Pod configuration
- using volumes

This parameter value could be also full configuration file, e.g. you have ngnix container and want to 'inject' whole configuration by using ConfigMap.

## Using config file
Example configmap file:
$ cat <<EOF > app.properties
driver=jdbc
database=postgres
lookandfeel=1
otherparams=xyz
param.with.hierarchy=xyz
EOF

to create configmap using this file: **kubectl create configmap app-config --from-file=app.properties**

Now we can create Pod that exposes the configmap using a volume (*deployments/config-map/helloworld-config-as-file.yml*)

Values will be listed under e.g. for driver in /etc/config/driver, for param.with.hierarchy in /etc/config/param/with/hierarchy

Example of using configfile as env variables is in *deployments/config-map/helloworld-config-as-env.yml* manifest.

## Example of nginx
In the file *deployments/config-map/reverseproxy.conf* we have example nginx configuration. </br>
To create new config map from this file: **kubectl create configmap nginx-config --from-file=config-map/reverseproxy.conf**

To get this configmap in yaml format we can execute **kubectl get configmap nginx-config -o yaml**

Example Pod contains of nginx and k8s-demo containers, manifest is in the file *deployments/config-map/nginx.yml*. Service manifest is in the nginx-service.yml file.

After creating Pod and Service we can curl service:
**curl SERVICE_URL -vvvv**, example response:
```
* Rebuilt URL to: http://IP_ADDRESS:30592/
*   Trying IP_ADDRESS...
* TCP_NODELAY set
* Connected to IP_ADDRESS (IP_ADDRESS) port 30592 (#0)
> GET / HTTP/1.1
> Host: IP_ADDRESS:30592
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Server: nginx/1.11.13
< Date: Tue, 17 Oct 2017 11:13:12 GMT
< Content-Type: text/html; charset=utf-8
< Content-Length: 12
< Connection: keep-alive
< X-Powered-By: Express
< ETag: ZZW/sd"ascasd-7Qdsddddd"
<
* Connection #0 to host IP_ADDRESS left intact
Hello World!⏎
```

Like we see request is going to node app through our configured nginx reverse proxy.

To go to nginx container bash:
**kubectl exec -it helloworld-nginx -c nginx -- bash**
