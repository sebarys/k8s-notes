# Kubernetes on AWS using kops (kubernetes operations)

To allow kops to deploy kubernetes we need to create new user in Amazon web console ( *WebConsole -> IAM -> Users -> Create new Users* ) </br>
Required IAM permissions
- AmazonEC2FullAccess
- AmazonRoute53FullAccess
- AmazonS3FullAccess
- IAMFullAccess
- AmazonVPCFullAccess

## Configure aws

1. Configure aws cli
`aws configure --profile {profileName}` add configuration </br> ( *In aws cli to use selected profile for multiple command: `export AWS_PROFILE={profile name}`* )

2. Create S3 bucket to store kops state

3. Create DNS domain
 - you need to create domain e.g. here for free http://www.dot.tk/en/index.html?lang=en (I made sebarys.tk)
 - in amazon route 53 create hosted zone (it cost 0.5$ per month) e.g. kubernetes.sebarys.tk
 - take your hosted zones values and add as nameservers in your domain in freenom (remove dots from the end of nameservers https://my.freenom.com/knowledgebase.php?action=displayarticle&id=3)

## Create cluster using kops

Make sure that you have generated ssh keys.

To create cluster execute following command: </br>
`kops create cluster --name={fullNameFromRoute53HostedZone} --state=s3://{s3BucketName} --zones={zoneName} --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone={fullNameFromRoute53HostedZone}`

**NOTE: aws cli need to have set proper configuration `export AWS_PROFILE={configName}`**

We can made some changes in configuration by execute `kops edit cluster {clusteName} --state=s3://{s3BucketName}}`

To accept configuration and finally build cluster execute: `kops update cluster {clusteName} --yes`

In `~/.kube/config` we can find your credentials to access to cluster.
By executing `kubectl get node` we can inspect if cluster already started (it takes a few minutes).

Now we can try to run the same Pod like in minikube (echoserver): </br>
`kubectl run hello-minikube --image=gcr.io/google_containers/echoserver:1.4 --port=8080`

After that we can expose this Pod by service: </br>
`kubectl expose deployment hello-minikube --type=NodePort`. </br>
To get port on which service is exposed execute `kubectl get services` and get value of port. Next go to VPC in Amazon WebConsole and open connection to this port in security group.

If everything was set properly you should see echoserver output in http://api.kubernetes.{ourDomain}:{servicePort}/

To shut down your EC2, Volumes etc. in AWS you need to delete cluster using kops: </br>
`kops delete cluster --name {clusteName} --state=s3://{s3BucketName}` </br>
To confirm add --yes to command and execute again.
