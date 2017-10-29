`kubectl get pods` Get information about running pods </br>
`kubectl describe pod {pod}` Describe given pod </br>
`kubectl expose pod {pod} --port={port} --name={name}` expose the port of a pod (creates a new service) </br>
`kubectl pod-forward {pod} {port}` forward the exposed pod port to your local machine </br>
`kubectl attach {pod} -i` attach to the pod, if process is loggins something it will be list in console </br>
`kubectl exec -it {pod} {command}` execute the command in the pod </br>
`kubectl exec -it {pod} bash` start bash session in container </br>
`kubectl labels pods {pod} mylabel=awesome` add a new label to the pod </br>
`kubectl get deployment` list deployments </br>
`kubectl get rs` get information about current ReplicaSets </br>
`kubectl get pods --show-labels` get pods with attached labels </br>
`kubectl rollout status deployment/{deploymentName}` get information about given deployment status </br>
`kubectl rollout history deployment/{deploymentName}` rollback to previous version </br>
`kubectl rollout history deployment/{deploymentName} --to-revision=n` rollback to specified version </br>
`kubectl rollout undo deployment/{deploymentName}` get information about given deployment status </br>
`kubectl set image deployment/{deploymentName} {containerName}={imageName}:{imageVersion}` set container image </br>
`kubectl edit deployment/{deploymentName}` edit deployment object </br>


`kubectl convert -f pod.yaml --local -o json` Convert the live state of the resource specified by 'pod.yaml' to the latest version and print to stdout in json format.
