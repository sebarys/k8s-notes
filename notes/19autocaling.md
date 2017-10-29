# Autoscaling

Kubernetes can autoscale Pods based on metrics. Automatically scaled can be Deployment, ReplicationController, or ReplicaSet

Autoscaling will periodically query your Pods, by default 30sec, can be changed by setting `--horizontal-pod-autoscaler-sync-period` flag value when launching the controller-manager.

Autoscaling will use `heapster`.

To test autoscaling we can create pod with requested 200milicpu(milicores) - 20% of a CPU core of the running node. If node will have 2 cores it is still 20% of a single core. Manifest is available in *deployments/autoscaling/hpa-example.yml*

Example HorizontalPodAutoscaler specification is in the file *deployments/autoscaling/hpa-example.yml*
We set targetCPUUtilizationPercentage to 50%, so it will be 200milicpu * 50% = 100milicpu


All needed components have their manifests in *deployments/autoscaling/hpa-example.yml*

To see available HorizontalPodAutoscaler execute `kubectl get hpa`

Next we will create in Route53 new entry e.g. *hpa.kubernetes.sebarys.tk*
To create some load we will run busybox
```
$ kubectl run -i --tty load-generator --image=busybox /bin/sh

while true; do wget -q -O- http://hpa.kubernetes.sebarys.tk:31001; done
```

After some time we can observe that load on our hpa-example instances is growing and hpa will scale up.
When we stop script hpa will scale down hpa-example instances down.
