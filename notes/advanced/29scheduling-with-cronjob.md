# Scheduling with CronJobs

CronJob can schedule Job on specified time, once or recurrently (e.g. each Monday at 9am).

CronJob uses Linux crontab time notation:
```
* * * * *

In order:
- minute ( 0 - 59 )
- hour ( 0 - 23 )
- day of month ( 0 - 31 )
- month ( 0 - 12 )
- day of week ( 0 - 6 Sunday to Saturday, 7 often is also Sunday )
```
e.g. </br>
`20 3 * * *` -> everyday on 3:20am </br>
`*/15 * * * *` -> every 15min

Example of CronJob:
```
kind: CronJob
apiVersion: batch/v1beta1
metadata:
  name: hello
spec:
  schedule: "25 3 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: my-cronjob
            image: busybox
            args:
            - /bin/sh
            - -c
            - echo This command runs every night at 3:25am
          restartPolicy: OnFailure
```
