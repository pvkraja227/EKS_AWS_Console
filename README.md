AWS Console:

### Step 1: Create IAM Role for EKS Cluster

IAM: 

Create Role:

service: EKS / EKS - Cluster / next / (Policies default) next / next

Role Name: eksclusterrole

create role

### Step 2: Create Dedicated VPC for the EKS Cluster

CloudFormation:

Create Stack

Choose an existing template

Upload a template file

uploadfile amazon-eks-vpc-private-subnets.yaml / next /

Provide a stack name: eks-stack-vpc

create stack

wait until - create complete

### Step 3: Create EKS Cluster

EKS:

Create cluster

Cluster configuration: Name: eks-cluster / Cluster service role: eksclusterrole

Kubernetes version: 1.30 / next

choose VPC / Security groups

Cluster endpoint access: check <Public and private> / next / next / create

(takes 12 - 15 min)

### Step 4: Install and Setup: IAM Authenticator & Kubectl utility

EC2: create an instance

(Install AWS CLI)

sudo apt install unzip

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install

/usr/local/bin/aws --version

aws configure (provide acccess keys)

aws iam list-users (check)

aws sts get-caller-identity (check)

(Install IAM Authenticator)

curl -o aws-iam-authenticator https://s3.us-west-2.amazonaws.com/amazoneks/1.16.8/2020-04-16/bin/linux/amd64/aws-iam-authenticator

chmod +x ./aws-iam-authenticator

mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$PATH:$HOME/bin

aws-iam-authenticator -help (check, neglect line 1 errors)

(Install Kubectl)

curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.7/2020-07-08/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin

kubectl version --short --client

aws eks --region ap-southeast-2 update-kubeconfig --name eks-cluster

// more /home/ubuntu/.kube/config (optional, more info of the cluster)

kubectl get svc (default - 1)

kubectl get nodes (0)

kubectl get ns (default - 4)


### Step 5: Creating IAM Role for EKS Worker Nodes

IAM:

create role

service: EC2 Policy: 

search: eks

1. AmazonEKS_CNI_Policy

2. AmazonEKSWorkerNodePoicy

search: ec2container

3. AmazonEC2ContainerRegistryReadOnly / next

name: eksworkernode

### Step 6: Creating Worker Nodes

EKS:

cluster/eks-cluster/compute/add node group/name: eks-worker-node-group/Role: eksworkernode / next / next

Configure remote access to nodes: check (select key value pair)

create (takes 10-15 min)

check EC2: 2 x t3.med are created as worker nodes

kubectl get nodes --watch (2)

kubectl get pods (0)

kubectl get svc (default - 1)

kubectl get deployment (0)

### Step 7: Deploy an application

git clone https://github.com/pvkraja227/EKS_AWS_Console.git

kubectl apply -f knote.yaml

kubectl apply -f mongo.yaml

kubectl get pods (2)

kubectl get svc (default, NodePort, LoadBalancer)

curl LoadBalancer link

chrome: LoadBalancer link

