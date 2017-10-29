# Web UI in Kubernetes

Kubernetes provide web interface to manage created cluster.
You can access this web managament console in `https://{masterNode}/ui` </br>
If for some reason it is not available you can run it manually by creating it from manifest </br>
`kubectl create -f https://rawgit.com/kubernetes/dashboard/master/src/deploy/kkubernetes-dashboard.yml`

If password is required you can find it by executing `kubectl config view`

During using minikube you can open dashboard by executing command `minikube dashboard` or get url by `minikube dashboard --url`
