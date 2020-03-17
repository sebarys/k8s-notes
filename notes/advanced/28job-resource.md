# Job Resource

If you want run a process that need to do its tasks and exit you can run Job.

There are three main types of Jobs:
- non parallel jobs - if pod failed it will be restarted if it finish successfully the Job will be completed
```
kind: Job
apiVersion: batch/v1
metadata:
  name: pi
spec:
  template:
    metadata:
      name: pi
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl", "-Mbignum=bpi", "-wle", "print bpi(2000)" ]
      restartPolicy: Never
```
- parallel jobs with fixed completion count - Job can run multiple Pods in parallel, you must specify fixed completion count. Job is complete when completion count = successfully exited Pods
```
kind: Job
apiVersion: batch/v1
metadata:
  name: pi
spec:
  template:
    metadata:
      name: pi
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl", "-Mbignum=bpi", "-wle", "print bpi(2000)" ]
      restartPolicy: Never
  completions: 2
```
- parallel jobs with work queue - you need to do some work so spawn job with parallelism = 10. Now k8s will run 10 Pods to this work and when any of them will exit successfully it means that Job completed.
