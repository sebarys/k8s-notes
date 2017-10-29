# High availability

To ensure HA:
- at least run 3 etcd nodes
- replicated API servers with a LoadBalancer
- running multiple instances of the scheduler and the controllers


## HA using kops
`kkops create cluster --name=kubernetes.sebarys.tk --state=s3://kops-state-94sr11 --zones=eu-central-1a,eu-central-1b,eu-central-1c --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.sebarys.tk --master-zones=eu-central-1a,eu-central-1b,eu-central-1c`

Master node will be replicated on each zone to ensure HA.
