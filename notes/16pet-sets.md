# Stateful sets

Stateful sets are stateful distributed apps on a Kubernetes cluster. </br>
This feature enable ability to run stateful applications that have stable pods hostname - normally end of pod name is randomised, it gives ability to have index as a postfix. </br>
It is also useful if you requires multiple pods with volumes based on their ordinal number or hostname, e.g. Cassandra/ElasticSearch cluster use DNS to find other members of the cluster.
