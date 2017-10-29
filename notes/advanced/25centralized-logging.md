# Centralized logging

If we want to look in logs for more than one Pod at once, we should think about log aggregation. </br>
In this example we will use:
- Fluentd for log forwarding
- ElasticSearch for log indexing
- Kibana for visualisation
- LogTrail - easy to use UI (plugin for Kibana)

 To create cluster on AWS using kops execute following commands:
 - `export AWS_PROFILE=my-private` </br>
 - `kops create cluster --name=kubernetes.sebarys.tk --state=s3://kops-state-94sr11 --zones=eu-central-1a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.sebarys.tk --yes`


 In file *deployments/logging/es-statefulset.yml* is manifest with autorization roles and declaration of ElasticSearch StatefulSet. We are using there volume that StorageClass is defined in *deployments/logging/storage.yml*
 ES will be exposed by Service declared in *deployments/logging/es-service.yml*

 In file *deployments/logging/fluentd-es-ds.yml* we have configuration of fluentd, it will be created as DeamonSet which means that each node will have exactly one instance of fluentd. </br>
 We mount **varlibdockercontainers** volume which give permission to fluentd to access folder with logs on this physical machine. Fluentd configuration will be mount from configMap.

```
 nodeSelector:
   beta.kubernetes.io/fluentd-ds-ready: "true"
```
Node selector means that this DeamonSet will be running only on nodes with set that selector as a label. In file *deployments/logging/fluentd-es-configmap.yml* we have fluentd configuration as configMap.

To display logs we will use Kibana. In file *deployments/logging/kibana-deployment.yml* is Kibana deployemtn manifest. Also we create service to expose Kibana.
