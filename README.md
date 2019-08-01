Here are the steps to install Kubernetes cluster using Kops in AWS with a pubic hosted zone in AWS.

link to kops https://github.com/kubernetes/kops

User who will create the cluster must have the following IAM permissions:
1. AmazonEC2FullAccess
2. AmazonRoute53FullAccess
3. AmazonS3FullAccess
4. IAMFullAccess
5. AmazonVPCFullAccess

Kops will create the following in your AWS account, make sure to delete the cluster to avoid charges.

#

Setup aws cli to interact with aws account

$ aws configure

Install kops and kubectl interact with the cluster

$ brew update && brew install kops kubectl

Run dig against your hosted zone, make sure you get an answer with ns.

$ dig ns k8.princedreamcars.com

Create bucket with the change the name of the bucket

$ aws s3api create-bucket --bucket jenkins-k8-princedreamcars-com-state-store --region us-east-1

$ aws s3api put-bucket-versioning --bucket jenkins-k8-princedreamcars-com-state-store  --versioning-configuration Status=Enabled

$ export NAME=k8.princedreamcars.com

$ export KOPS_STATE_STORE=s3://jenkins-k8-princedreamcars-com-state-store

**We can then spin up our cluster using:**

$ kops create cluster --cloud=aws --zones=us-east-1a --name=jenkins.k8.princedreamcars.com --dns-zone=k8.princedreamcars.com --dns=public --state=s3://jenkins-k8-princedreamcars-com-state-store --cloud-labels="Stack=Test,App=web"

$ kops update cluster ${NAME} --yes

$ kops validate cluster

$ kubectl get nodes --show-lables

$ kops delete cluster --name ${KOPS_CLUSTER_NAME} --yes




__________________________________________________________________________________________________________________________
$ kops create cluster --node-count=4 --node-size=t2.medium --cloud=aws --zones=us-east-1a --name=<*****nameyoucluster****.k8s.local> --cloud=aws --cloud-labels="Stack=Test,Application=Frontend"

$ kops create cluster --name=<*****nameyoucluster****.k8s.local> --state=s3://<****same-bucket-name***> --zones=us-east-1a --node-count=2 --networking weave --topology private --bastion="true"

$ kops create cluster --node-count=2 --node-size=t2.medium --zones=us-east-1a

$ kops create cluster --name=mohsin.k8s.local --state=s3://mohsin-kops-state-bucket --zones=us-east-1a --node-count=2 --node-size=t2.medium --networking=weave --cloud=aws --cloud-labels="Stack=Test,App=web"

$ kops create cluster --cloud=aws --zones=us-east-1a --name=jenkins.k8.princedreamcars.com --dns-zone=k8.princedreamcars.com --dns public --state=s3://jenkins-k8-princedreamcars-com-state-store
