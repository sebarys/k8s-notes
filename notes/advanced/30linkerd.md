# Linkerd

Linkerd helps to manage mesh-service inside a cluster. It is a transparent proxy that adds:
- service discovery
- routing
  - latency aware routing
  - can shift traffic to canary deployment
- failure handling (retries, circuit breaking)

Linkerd need to be running on each node as DaemonSet

It is completely transparent from Pod and clients point of view.
