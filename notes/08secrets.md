# Secrets in Kubernetes

Secrets provides a way to distribute credentials or secret data to Pods. Even Kubernetes use this mechanism itself to provide to Pods access to internal API.

This mechanism is not only one, you can still use external mechanism to provide credentials to your Pods.

Secrets can be used as:
- env variables
- as a file in Pod - require volumes which store files attached to your Pod
- still you can use external private image to pull images

To generate Secret using files you can execute following commands:
```
$ echo -n "root" > ./username.txt
$ echo -n "password" > ./password.txt
$ kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
secret "db-user-pass" created
```

To generate Secret using SSH key or SSL cert
```
$ kubectl create secret generic ssl-certificate --from-file=ssh-privatekey=~/.ssh/id_rsa --ssl-cert-=ssl-cert=mysslcert.crt
```

Also you can create secrets from manifests.

To use secrets as env variable in Pod:
```
  ...
  spec:
    containers:
    - name: ...
      ...
      env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: db-secret
            key: username
      - name: SECRET_PASSWORD
      ...
```

To provide secrets in file (in this example for two files: username.txt and password.txt they will be stored in /etc/credentials/db-secrets/username|password):
```
...
spec:
  containers:
  - name: ...
    ...
    volumeMounts:
    - name: credentialsvolume
      mountPath: /etc/credentials
      readOnly: true
  volumes:
  - name credentialsvolume
    secret:
      secretName: db-secrets

    ...
```

## Secrets as volumes

When create secret from manifest in *deployments/helloworld-sercrets.yml* you can check by executing `kubectl get secrets` that it is available.

Next we can create deployment from *deployments/helloworld-sercrets-volumes.yml* which Pods are using created secrets. </br>
When we describe one of the Pods it will contain information about new volume specified by us in manifest. To verify if correct values are available in Pods we can go to container by `kubect exec -it {podName} bash` and read files content.
