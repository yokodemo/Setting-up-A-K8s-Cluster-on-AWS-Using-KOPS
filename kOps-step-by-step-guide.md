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

## 2) create kops user
``` sh
 sudo adduser kops
 sudo echo "kops  ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/kops
 sudo su - kops
 ```
 ##  2a) install AWSCLI using the apt package manager
  ```sh
 curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
 unzip awscliv2.zip
 sudo ./aws/install 
 ```
 ## or 2b) install AWSCLI using the script below
 ```sh
 sudo apt update -y
 sudo apt install unzip wget -y
 sudo curl https://s3.amazonaws.com/aws-cli/awscli-bundle.zip -o awscli-bundle.zip
 sudo apt install unzip python -y
 sudo unzip awscli-bundle.zip
 sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
 ```
## 3) Install kops software on an ubuntu instance by running the commands below:
 	sudo apt install wget -y
 	sudo wget https://github.com/kubernetes/kops/releases/download/v1.22.0/kops-linux-amd64
 	sudo chmod +x kops-linux-amd64
 	sudo mv kops-linux-amd64 /usr/local/bin/kops
 
## 4) Install kubectl kubernetes client if it is not already installed
```sh
 sudo curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
 sudo chmod +x ./kubectl
 sudo mv ./kubectl /usr/local/bin/kubectl
```
## 5) Create an IAM role from AWS Console or CLI with the below Policies. 

![Image](https://github.com/user-attachments/assets/78aa5400-ff02-4ac1-8e47-c9e546a1960a)

## 6) create an S3 bucket
## Execute the commands below in your KOPS control Server. use unique s3 bucket name. If you get bucket name exists error.
	aws s3 mb s3://yokonew
	aws s3 ls # to verify
	
 ## 6b) create an S3 bucket    
	Expose environment variable:
    # Add env variables in bashrc
    
       vi .bashrc
	# Give Unique Name And S3 Bucket which you created.
	export NAME=yokonew.k8s.local
	export KOPS_STATE_STORE=s3://yokonew
 
      source .bashrc  

 ![Image](https://github.com/user-attachments/assets/1c142b26-b73f-4a57-9677-e5589159b406)
 
