# Assignemnt

# Pre-requisites:
   
   
    - Install Docker
	- EKS Cluster

# Install Docker:
    yum install docker -y
    service docker start

# Add jenkins user docker group
    usermod -aG docker jenkins


# EKS Cluster Setup:
  Step1: Take EC2 Instance with t2.xlarge instance type

Step2: Create IAM Role with Admin policy for eks-cluster and attach to ec2-instance
 #Kubectl : client in order to apply k8s commands in order to connect to cluster
Step3: Install kubectl

curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/kubectl
chmod +x ./kubectl
mkdir -p $HOME/bin
cp ./kubectl $HOME/bin/kubectl
export PATH=$HOME/bin:$PATH
echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
source $HOME/.bashrc
kubectl version --short --client

Step4: Install eksctl:
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/bin
eksctl version

Step5: Cluster creation:
eksctl create cluster --name=eksdemo \
                  --region=us-east-1 \
                  --zones=us-east-1a,us-east-1b \
                  --without-nodegroup 


Step6: Add Iam-Oidc-Providers:
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster eksdemo \
    --approve

Step7: Create node-group:

eksctl create nodegroup --cluster=eksdemo \
                   --region=us-east-1 \
                   --name=eksdemo-ng-public \
                   --node-type=t2.medium \
                   --nodes=2 \
                   --nodes-min=2 \
                   --nodes-max=4 \
                   --node-volume-size=10 \
                   --ssh-access \
                   --ssh-public-key=vamsi-devops \
                   --managed \
                   --asg-access \
                   --external-dns-access \
                   --full-ecr-access \
                   --appmesh-access \
                   --alb-ingress-access	

# Add kubectl to default path
    cp ./kubectl /usr/bin
	
# Make a dockerfile

# Docker Image build and Docker push Image
Docker Login
Username : Enter username
Password : Enter Password
Login Successful

# Build docker image
docler image build -t akashnewrepo/nodejs .
. stands for dockerfile in current directory 
otherwise if in another directory need to provide thatr path

docker images : Shows imagews build 

# Push docker image to dockerrepository
docker image push akashnewrepo/nodejs

Check in docker repo if it is pushed successfully or not


######################################################Kunernetes###############################################

We need to define Deployment, Service file

#Deployment file
Deployment file :Pods will be created.In pods containers are running and inside container application is running

#Service file
Service file :In order to allow traffic from outside to EKS cluster we are using LoadBalncer type service

kubectl apply -f deployment.yaml

kubelctl get pods -o wide : Running pods shows

kubectl apply -f service.yaml ( You can control port via NodePort)
kubectl get svc : Services created showing

In aws make sure LoadBalancer are InService

I could also use ingress file if i have multiple Microservices


You will receive http://ad5ab72d8d77042768e8994647f637e1-1324271138.us-east-1.elb.amazonaws.com  this to access your application from outside


# Goto Web UI and Check output of application
   http://ad5ab72d8d77042768e8994647f637e1-1324271138.us-east-1.elb.amazonaws.com:8080/
   
 