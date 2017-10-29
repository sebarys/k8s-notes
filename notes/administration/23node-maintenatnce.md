# Node maintenance

Node controller is responsible for managing the Node objects.
- assign IP space
- keeps node list up to date
- monitoring heath of the nodes
- reschedule Pods on healthy nodes

When you adding new node, the kubelet will attempt to register itself (self-registration).

To decommission node gracefully:
 - `kubectl drain NODE_NAME --grace-period=600`
 - if after some time on node runs pods not managed by controller `kubectl drain NODE_NAME --force`
