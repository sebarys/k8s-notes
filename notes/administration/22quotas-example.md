# Quotas example

In *deployments/quotas/resourcequotas.yml* is example of quotas for namespace 'myspace'.

After creating namespace and quotas when we try to create deployment from file *deployments/quotas/helloworld-no-quotas.yml* no pods will be created.

When create deployment from manifest *deployments/quotas/helloworld-with-quotas.yml* pods will be creating, but only two because we reach the limit. We could investigate this by executing:
```
$ kubectl get rs --namespace=myspace
$ kubectl describe rs helloworld-deployment-693040999 --namespace=myspace
$ kubectl get quota --namespace=myspace
$ kubectl describe quota/compute-quota --namespace=myspace
```

We can specify default values of quotas like in the *deployments/quotas/defaults.yml* manifest.
We can check existing limits by executing:
```
$ kubectl get limits --namespace=myspace
$ kubectl describe limits LIMIT_NAME --namespace=myspace
```

After creating default quotas we can create deployment from helloworld-no-quotas.yml and pods will start normally.
