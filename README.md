# Steps - Environment setup

## Apply the ./iac/vpc.yml file:
aws cloudformation create-stack --stack-name EKSVPC --template-body file://vpc.yml --capabilities CAPABILITY_NAMED_IAM

## Apply the ./iac/eks.yml file:
aws cloudformation create-stack --stack-name EKSFargateCluster --template-body file://eks.yml --capabilities CAPABILITY_NAMED_IAM

## Patch the coredns to add tolerations
kubectl -n kube-system patch deployment coredns --type=json -p='[{"op": "add", "path": "/spec/template/spec/tolerations", "value": [{"key": "eks.amazonaws.com/compute-type", "operator": "Equal", "value": "fargate", "effect": "NoSchedule"}]}]'

## Install AWS ALB controller
Follow the instruction in the url Below:
https://docs.aws.amazon.com/eks/latest/userguide/lbc-helm.html

NOTE: Edit the cluster name and the AWS account ID to yours


# Steps - App Deployment

## Open ./github/workflows/config.yml, replace the values below with your details
  AWS_REGION: <region>
  AWS_ACCOUNT_ID: <aws-account-id>  
  ECR_REPOSITORY: <full-ECR-URL>
  EKS_CLUSTER_NAME: <your-clustyer-name>
  Also, store AWS access key and secret key as a git secret

  ## Push the code to you repository. 
  This will build the docker image, tag and upload the image to ECR. It will also pull the image and deploy to you kubernetes cluster

## Test your deploy
Go to EC2, Locate loadbalancer, access the loadbalancer DNS from your browser
