Prerequisites to deploy the application:

kubectl – A command line tool for working with Kubernetes clusters. For more information, see Installing or updating kubectl.

eksctl – A command line tool for working with EKS clusters that automates many individual tasks. For more information, see Installing or updating.

AWS CLI – A command line tool for working with AWS services, including Amazon EKS. For more information, see Installing, updating, and uninstalling the AWS CLI in the AWS Command Line Interface User Guide. After installing the AWS CLI, we recommend that you also configure it. For more information, see Quick configuration with aws configure in the AWS Command Line Interface User Guide.


Steps to follow:

Install EKS using Fargate
eksctl create cluster --name demo-cluster --region us-east-1 --fargate

Configure IAM OIDC provider
eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve

Setup ALB Controller:
Download IAM policy
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json

Create IAM Policy
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

Create IAM Role
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

Deploy ALB controller
Add helm repo
helm repo add eks https://aws.github.io/eks-charts

Update the repo
helm repo update eks

Install
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
  -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<region> \
  --set vpcId=<your-vpc-id>


Verify that the deployments are running.
kubectl get deployment -n kube-system aws-load-balancer-controller


Deploy the deployment, service and Ingress (All the deployments are together here)
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml

https://private-user-images.githubusercontent.com/43399466/258144320-93b06a9f-67f9-404f-b0ad-18e3095b7353.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTE3Nzc0MzUsIm5iZiI6MTcxMTc3NzEzNSwicGF0aCI6Ii80MzM5OTQ2Ni8yNTgxNDQzMjAtOTNiMDZhOWYtNjdmOS00MDRmLWIwYWQtMThlMzA5NWI3MzUzLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAzMzAlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMzMwVDA1Mzg1NVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWFlOGM3YzhmMTkyMTVhMzlmYmY5NTQxNWIyMDUxMDc5ZjUxZjlhYjIwNmQxYmM4YmQ5M2JiODk2MTA1YTcwMTkmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.A-Z5K6_Cx4A8kcqO-G0kYZa3mEoUi2NPr7ZC3jZYX0Q







