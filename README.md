# K8

Here are the steps to install Kubernetes cluster using Kops in AWS.

link to kops https://github.com/kubernetes/kops

User who will create the cluster must have the following IAM permissions:
1. AmazonEC2FullAccess
2. AmazonRoute53FullAccess
3. AmazonS3FullAccess
4. IAMFullAccess
5. AmazonVPCFullAccess

Kops will create the following in your AWS account, make sure to delete the cluster to avoid charges.

#
#
#
#
#

Install kubectl to interact with the cluster
Using Brew : brew install kubernetes-cli
Using Curl : curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl

 If used curl add kubectl to PATH  

$ aws configure
AWS Access Key ID [None]: Access_key
AWS Secret Access Key [None]: Secret_key
Default region name [None]: us-east-1
Default output format [None]:

$ brew update && brew install kops kubectl

$ aws s3api create-bucket --bucket <****create-kops-state-bucket***> --region us-east-1

$ aws s3api put-bucket-versioning --bucket <****same-bucket-name***>  --versioning-configuration Status=Enabled

$ export KOPS_CLUSTER_NAME=<*****nameyoucluster****.k8s.local>

$ export KOPS_STATE_STORE=s3://<****same as bucket name***>

**Feel free to remove or change anything in the create command to fit your needs.**

The below commands will create a cluster configuration with in AWS with t2.small instances (Use any of the 3 command below to create)

$ kops create cluster --node-count=4 --node-size=t2.small --cloud=aws --zones=eu-central-1a --name=<*****nameyoucluster****.k8s.local> --cloud=aws -----cloud-labels="Stack=Prod,ApplicationName=Frontend"

$ kops create cluster --name=<*****nameyoucluster****.k8s.local> --state=s3://<****same-bucket-name***> --zones=us-east-1a --node-count=2 --networking weave --topology private --bastion="true"

$ kops create cluster --node-count=2 --node-size=t2.medium --zones=us-east-1a

*We can then spin up our cluster using:*

$ kops update cluster --name ${KOPS_CLUSTER_NAME} --yes

$ kops validate cluster

$ kubectl get nodes --show-lables

$ kops delete cluster --name ${KOPS_CLUSTER_NAME} --yes
