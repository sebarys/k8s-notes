# Labels in Kubernetes

Labels are in key/value pairs and are used to tag resoruces. They are not unique and multiple labels can be added to one object. Thanks to them you can use filters an use label selectors to specify one group of objects.

Previously we used labels to tag pods. To run pods only in specified nodes you can tag a node and use nodeSelector in Pod configuration.

`kubectl label nodes {nodeName} {labelKey}={labelValue}` add label to node </br>
To run Pod in specified node in manifest spec add
```
  ...
  spec:
  ...
    nodeSelector:
      {labelKey}: {labelValue}

```
In *deployments/helloworld-nodeselector.yml* file contain manifest with specification that Pods should be initialized on node with label *hardware: high-spec*.

To show nodes labels `kubectl get nodes --show-labels`
Currently node don't have proper label, so when you try to create Pods from manifest they will be all created but not available (Pod is trying to start but fail due MatchNodeSelector error).

After adding proper label to node by `kubectl label nodes minikube hardware=high-spec` Pods will be running correctly.
