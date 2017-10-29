# Resource Usage Monitoring

Heapster enables Container Cluster Monitoring and Performance Analysis, it is a prerequisite if you want to do pod auto-scaling.

Heapster exports cluster metrics via REST endpoints. You can use different backends (InfluxDB, Google Cloud Monitoring, Kafka ...)

Example manifest of Heapster: `https://github.com/kubernetes/heapster/tree/master/deploy/kube-config/influxdb`

Overview of architecture:

<p align="left"><img src="img/resource-usage-monitoring.png"/></p>


To create monitoring services create from manifests in *deployments/resource-usage-monitoring*

In Route 53 create record set which will be an alias for grafana LoadBalancer.

Under created record set grafana is available, you can log to default user admin:admin
