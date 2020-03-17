# Volumes

Kubernetes allows you to store data outside the container to allow to keep state of application. Persistent Volumes allows you attach a volume to a container that will exists even when the container stops.

Volumes can be attached using different volume plugins.
<p align="left"><img src="img/volumes.jpg"/></p>

## Example

First we need to create volume on AWS by executing following steps:
1. **export AWS_PROFILE=MY_PROFILE** (e.g. MY_PROFILE = my-private)
2. **aws ec2 create-volume --size 10 --region eu-central-1a --availability-zone eu-west-1a --volume-type gp2**

Created volume:
```
{
    "AvailabilityZone": "eu-central-1a",
    "Encrypted": false,
    "VolumeType": "gp2",
    "VolumeId": "vol-Id",
    "State": "creating",
    "Iops": 100,
    "SnapshotId": "",
    "CreateTime": "2017-10-17T12:56:29.120Z",
    "Size": 10
}
```

Example deployment manifest is in *deployments/volumes/helloworld-with-volume.yml* file.

To run kops AWS cluster:
**kops create cluster --name=kubernetes.sebarys.tk --state=s3://kops-state-94sr11 --zones=eu-central-1a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.sebarys.tk --yes**

Next to validate if cluster is running we can execute **kops validate cluster --state=s3://kops-state-94sr11** or **kubectl get nodes --show-labels**

Now we can create deployment with attached volume by run *kubectl create -f volumes/helloworld-with-volume.yml*

After deployment is up we can go into the container and add some file in the directory where persistent volume is mounted e.g. **echo 'test' > /myvol/myvol.txt**.

When we describe Pod we can check on which node it is running. To stop node execute **kubectl drain NODE_ID --force**. Pod from deployment will be restarted in another node. We can verify that value saved in mounted persistent volume is still available.

To clean up:
- **kubectl delete -f volumes/helloworld-with-volume.yml** delete deployment with attached volume
- **export AWS_PROFILE=my-private** and **aws ec2 delete-volume --volume-id vol-Id** delete created volume
- **kops delete cluster --name kubernetes.sebarys.tk --state=s3://kops-state-94sr11 --yes**

## Volume Provisioning

We need to specify storage class to enable provisioning volumes, example is under *deployments/volumes/provisioning-storage-class.yml*

Next we can create a volume claim with specified size, example is in the *deployments/volumes/my-volume-claim.yml*
*kubectl get pvc* to check if provisioned claim is created.

*volume.beta.kubernetes.io/storage-class: "standard"* because in storage-class we name it like that.

Pod manifest which provision volume using this claim is in *deployments/volumes/helloworld-with-provisioned.yml* file.
