## Setting up Kubernetes (K8s) Cluster on AWS Using KOPS

1.kops is a software use to create production ready k8s cluster in a cloud provider like AWS.

2. kOPS supports multiple cloud providers

3. Kops compete with managed kubernestes services like EKS, AKS and GKE

4. Kops is cheaper than the others.

5. Kops create production ready K8S.

6. KOPS create resources like: LoadBalancers, ASG, Launch Configuration, woker node Master node (CONTROL PLANE.

7. KOPS is IaaC

#!/bin/bash
## 1) Create Ubuntu EC2 instance in AWS

 ##  2a) install AWSCLI using the script below
  ```sh
 curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
 sudo apt install unzip wget -y
 sudo unzip awscliv2.zip
 sudo ./aws/install 
 ```
 
## 3) Install kops software on an ubuntu instance by running the commands below:
 	sudo wget https://github.com/kubernetes/kops/releases/download/v1.22.0/kops-linux-amd64
 	sudo chmod +x kops-linux-amd64
 	sudo mv kops-linux-amd64 /usr/local/bin/kops
 
## 4) Install kubectl kubernetes client if it is not already installed
```sh
 sudo curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
 sudo chmod +x ./kubectl
 sudo mv ./kubectl /usr/local/bin/kubectl
```
## 5) Create an IAM USER (used to access AWS recources). 
### This is the working method for AWS CLI commands. Kindly configure aws-cli packages for your Linux machines.
To build clusters within AWS, we'll create a dedicated IAM user for Kops. This user requires API credentials to use Kops. Create the user and credentials using the AWS console. The kops user will require the following IAM permissions to function properly: or You can create now with admin access for testing.

1. AmazonEC2FullAccess
2. AmazonRoute53FullAccess
3. AmazonS3FullAccess
4. IAMFullAccess
5. AmazonVPCFullAccess

## 6) Configure the AWS client to use your new IAM user
## Execute the commands below in your KOPS control Server. use unique s3 bucket name. If you get bucket name exists error.
```
aws configure
# Use your new access and secret key here

aws iam list-users
# you should see a list of all your IAM users here
```	
 ## 6a) Create an S3 bucket  
 ## Execute the commands below in your KOPS control Server. use unique s3 bucket name. If you get bucket name exists error.
 	aws s3 mb s3://yokonew
	aws s3 ls # to verify
 
 ## 6b) Give Unique Name And S3 Bucket which you created.
	export NAME=yokonewdemo.k8s.local
	export KOPS_STATE_STORE=s3://yokonew
 

 
 # 7) Create sshkeys before creating cluster
 ```sh
 # after running the below command press enter for all the keygen prompts
    ssh-keygen -t rsa

  # if you encounter [permission denied] when creating the public key then run:
    sudo chown -R kops:kops .ssh
 ```

 # 8) Create kubernetes cluster definitions on S3 bucket
```sh
kops create cluster --zones us-east-1a --networking weave --master-size t2.medium --master-count 1 --node-size t2.medium --node-count=2 ${NAME}
# copy the sshkey into your cluster to be able to access your kubernetes node from the kops server
kops create secret --name ${NAME} sshpublickey admin -i ~/.ssh/id_rsa.pub
```

# 9) Initialise your kops kubernetes cluser by running the command below
```sh
kops update cluster --name ${NAME} --yes --admin
```
# 10) Validate your cluster(KOPS will take some time to create cluster ,Execute below command after 10 or 15 mins)
```
kops validate cluster
	   
	   Suggestions:
 * validate cluster: kops validate cluster --wait 10m
 * list nodes: kubectl get nodes --show-labels
 * ssh to the master: ssh -i ~/.ssh/id_rsa ubuntu@ipaddress
 * the ubuntu user is specific to Ubuntu. If not using Ubuntu please use the appropriate user based on your OS.
 * read about installing addons at: https://kops.sigs.k8s.io/operations/addons.
```
## 11) - Export the kubeconfig file to manage your kubernetes cluster from a remote server. For this demo, Our remote server shall be our kops server 
```sh
 kops export kubecfg $NAME --admin
```
## 12) To list nodes and pod to ensure that you can make calls to the kubernetes apiSAerver and run workloads
	  kubectl get nodes 

### 12a) Alternative you can ssh into your kubernetes master server using the command below and manage your cluster from the master
    sh -i ~/.ssh/id_rsa ubuntu@ipAddress

### 12b) Alternative, Enable PasswordAuthentication in the master server and assign passwd
```sh
sudo sed -i "/^[^#]*PasswordAuthentication[[:space:]]no/c\PasswordAuthentication yes" /etc/ssh/sshd_config
sudo service sshd restart
sudo passwd ubuntu
```
 
## 13) To Delete Cluster

   kops delete cluster --name=${NAME} --state=${KOPS_STATE_STORE} --yes  
   
====================================================================================================


14 # IF you want to SSH to Kubernetes Master or Nodes Created by KOPS. You can SSH From KOPS_Server

sh -i ~/.ssh/id_rsa ubuntu@ipAddress

  
``
