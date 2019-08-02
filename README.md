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

$ aws s3api create-bucket --bucket k8-princedreamcars-com-state-store --region us-east-1

$ aws s3api put-bucket-versioning --bucket k8-princedreamcars-com-state-store  --versioning-configuration Status=Enabled

$ export KOPS_CLUSTER_NAME=k8.princedreamcars.com

$ export KOPS_STATE_STORE=s3://k8-princedreamcars-com-state-store

**Options for the Kops create command**

--master-size=

--master-count=

--node-size=

--node-count=

--cloud=

--zones=

--cloud-labels=

--bastion=

--topology=

--networking=

--state=

--dns-zone=

--dns=

**We create the cluster using the command below:**

$ kops create cluster --cloud=aws --zones=us-east-1a name=${KOPS_CLUSTER_NAME} --dns-zone=k8.princedreamcars.com --dns=public --networking=weave --cloud-labels="Stack=Test,App=web"

$ kops update cluster $KOPS_CLUSTER_NAME} --yes

$ kops validate cluster

$ kubectl get nodes --show-lables

$ kops delete cluster --name ${KOPS_CLUSTER_NAME} --yes
