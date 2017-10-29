# Daemon sets

Daemon sets ensure that every single node in the cluster runs the same pod resource. When you add new node Pod will be automatically started. On node remove Pod will not be rescheduled to other node.

Use cases:
- logging aggregators
- monitoring
- load balancers/ reverse proxy/ API gateways
- if you need one instance per one physical instance

Example daemon set manifest is in the file *deployments/deamon-set/daemon-set.yml*
