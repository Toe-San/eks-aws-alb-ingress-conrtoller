# eks-aws-alb-ingress-conrtoller
AWS ALB ingress controller  with EKS cluster
![image](https://github.com/user-attachments/assets/47034a3d-1483-4dec-9e49-87baf9e42102)



Tag your vpc public subnet.
            
            Key                    | value
            kubernetes.io/role/elb | 1
            
![image](https://github.com/user-attachments/assets/36c2ad0d-376f-4309-b417-455bae82211a)



Step 1: Create IAM Role using eksctl

1.1 Create IAM Policy using the iam_policy.json

    aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

1.2 Replace my-cluster with the name of your cluster, 111122223333 with your account ID, and then run the command. If your cluster is in the AWS GovCloud (US-East) or AWS GovCloud (US-West) AWS Regions, then replace arn:aws: with arn:aws-us-gov:.

    eksctl create iamserviceaccount \
    --cluster=my-cluster \
    --namespace=kube-system \
    --name=aws-load-balancer-controller \
    --role-name AmazonEKSLoadBalancerControllerRole \
    --attach-policy-arn=arn:aws:iam::111122223333:policy/AWSLoadBalancerControllerIAMPolicy \
    --approve

Step 2: Install AWS Load Balancer Controller
2.1 Add the eks-charts Helm chart repository.

    helm repo add eks https://aws.github.io/eks-charts

2.2 Update your local repo to make sure that you have the most recent charts.

    helm repo update eks

2.3 Install the AWS Load Balancer Controller.

    helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
    -n kube-system \
    --set clusterName=my-cluster \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller

Step 3: Verify that the controller is installed
3.1 Verify that the controller is installed.

    kubectl get deployment -n kube-system aws-load-balancer-controller

An example output is as follows.

    NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
    aws-load-balancer-controller   2/2     2            2           84s
