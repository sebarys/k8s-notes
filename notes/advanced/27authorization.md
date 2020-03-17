# Authorization

Access control is implemented on kube-apiserver level - when you want to process some commands your requests will be checked if you have access to execute it.

There are multiple authorization module:
- Node - internal, used by kubelets
- ABAC - attribute based access control. Access is controlled by policies, e.g. User X can do anything in namespace Z (it is not very granural)
- RBAC - role based access controll. Regulating access using roles. Dynamically configured permission policies.
- Webhook - sends authorization requests to external REST interface (allow to use own authorization implementation)

To enable authorization you need pass flag to apiserver during startup: `--authorization-mode=<VALUE>`, VALUE could be e.g. RBAC

By default you are allowing everything.

If you are running kops cluster you can change in configuration following line:
```
kubeAPIServer:
  authorizationMode: RBAC
```
or using minikube:
```
$ minikube start --extra-config=apiserver.Authorization.Mode=RBAC
```
(if you want to set more flag add another --extra-config=<VALUE>)

## RBAC
You can add RBAC resources to your cluster, all what you need to do i to describe it in yaml format and create. </br>
- define a role `Role` (single namespace) / `ClusterRole` (cluster-wide)
- assign users/groups to that role `RoleBinding` (single namespace)/ `ClusterRoleBinding` (cluster-wide)

Example: RBAC Role granting read to Pods and Secrets within default namespace
```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] //core groups, there are Pods
  resources: ["pods", "secrets"]
  verbs: ["get", "watch", "list"]
```

and the same but applied to whole cluster (ClusterRole)
```
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: pod-reader
rules:
- apiGroups: [""] //core groups, there are Pods
  resources: ["pods", "secrets"]
  verbs: ["get", "watch", "list"]
```

To assign role to user:
```
kind: RoleBinding
apiVersion:  rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: Bob
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

To try it out you can use auth0 and authorization extension.

There are some predefined roles:
- cluster-admin - super user access
- admin - has read/write access within namespaces but could not change quotas
- edit - read/write access to most objects within namespaces but could not view/create new roles/rolesbindings
- view - read access, but can't see any secrets, roles and rolebingins
