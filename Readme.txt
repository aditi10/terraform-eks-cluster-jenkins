Prerequisites
1. an AWS account
2. the AWS CLI, installed and configured
3. AWS IAM Authenticator
4. the Kubernetes CLI, also known as kubectl should be installed on system
4. Helm cli should be installed on system

Setup EKS cluster using terraform code:
1. creates VPC, subnets, Internetgateway and Nat gateway
2. Spins up EKS cluster with 3 nodes
3. Sets up security grouos for nodes and Eks cluster
4. creates IAM role and provides permission to it. Adds TLS certificates.
5. region update van be provided in variables.tf


git clone terraform-eks-cluster-jenkins
cd terraform-eks-cluster-jenkins
terraform init
terraform apply

aws eks --region $(terraform output -raw region) update-kubeconfig \
    --name $(terraform output -raw cluster_name)

Verify the Cluster

kubectl cluster-info
kubectl get nodes

To setup Kubernetes cluster refer link: https://learn.hashicorp.com/tutorials/terraform/eks

2. Install Jenkins master slave on Kubernetes cluster using helm and below steps:
- Create a loadbaalncer which helps accessing the jenkins
- Creates Jenkins master server using helm with the configurations
- Creates a jenkins namespace
kubectl create namespace jenkins
kubectl get ns

- Create Jenkins service account
cd jenkins
kubectl create -f jenkins-sa.yaml

- create a persistent volume
kubectl create -f jenkins-volume.yaml

- Updated values in "helm-charts/charts/jenkins/values.yaml"
cd helm-charts/charts/jenkins/
helm install jenkins -n jenkins -f values.yaml jenkinsci/jenkins


Refered link : https://www.bogotobogo.com/DevOps/Docker/Docker-Kubernetes-Jenkins-Helm.php and https://github.com/jenkinsci/helm-charts/tree/main/charts/jenkins

To access the Jenkins server follow below steps:

1. Get your 'admin' user password by running:
  kubectl exec --namespace jenkins -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/additional/chart-admin-password && echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  export NODE_PORT=$(kubectl get --namespace jenkins -o jsonpath="{.spec.ports[0].nodePort}" services jenkins)
  export NODE_IP=$(kubectl get nodes --namespace jenkins -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT/login

Install Nodejs plugin in jenkins and setup pipeline in Jenkins using "Jenkinsfile"
- Once the jenkins server is up, we need to install NodeJS plugin in it
- Craete a pipeline Job and add the Jenkinsfile
- Install aws cli plugin on Jenkins, and user has permission to run it

The Jenkinsfile
1. Clones the git hub repository
2. Executes the npm install command
3. Downloads Hugo 0.91 version and execuutes it
4. Runs the server using hugo command
5. Deploys the application to AWS

