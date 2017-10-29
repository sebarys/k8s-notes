# Health checks in Kubernetes

To detect and resolve problems with your applications you can run heath checks. There are two types of healthchecks:
- Running a command periodycally
- Periodic checks on URL

E.g. to add http check you only need specify it in manifest:
```
  ...
  containers:
  - name: ...
  ...
    livenessProbe:
      httpGet:
        path: /
        port: 3000
      initialDelaySeconds: 15
      timeoutSeconds: 30
```

After creating deployment from *deployments/helloworld-heathcheck.yml* file we can inspect Containers Liveness section in `kubectl describe pod` command output, example: </br>
`http-get http://:nodejs-port/ delay=15s timeout=30s period=10s #success=1 #failure=3` means that Pod will be checked by each 10s and will be recognized as healthy after one successfully request. After three failures it will be terminated and restarted.

`kubectl edit deployment {deploymentName}` - command allows to edit deployment (also helpful to saw how settings can be configured) 
