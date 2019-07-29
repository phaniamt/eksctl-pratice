# Ref 
https://eksctl.io/introduction/getting-started/

https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html

https://github.com/weaveworks/eksctl

https://github.com/kubernetes-sigs/aws-ebs-csi-driver

# Create cluster using eksctl

eksctl create cluster -f eks-cluster-create.yaml

# Check the cluster staus

eksctl utils describe-stacks --region=us-east-1 --name=basic-cluster

# Get the .kube/config file

aws eks --region us-east-1 update-kubeconfig --name basic-cluster


# To list the details about  all of the nodegroups, use:

eksctl get nodegroup --cluster=basic-cluster --region=us-east-1

# To list the details about a nodegroup , use:

eksctl get nodegroup --cluster=basic-cluster --name=kube-cluster-ng-1 --region=us-east-1

# The nodegroups ng-1-workers and ng-2-builders can be created with this command

eksctl create nodegroup --config-file=eks-node-group-create.yaml

# A nodegroup can be scaled by using the eksctl scale nodegroup command:

eksctl scale nodegroup --cluster=dev-cluster --nodes=3 --name=ng-2-builders

# Update labels

kubectl label nodes -l alpha.eksctl.io/nodegroup-name=ng-1 new-label=foo

# To delete a nodegroup, run:

eksctl delete nodegroup --cluster=<clusterName> --name=<nodegroupName>
  
NOTE: this will drain all pods from that nodegroup before the instances are deleted

 # but if you need to drain a nodegroup without deleting it, run:
 
eksctl drain nodegroup --cluster=<clusterName> --name=<nodegroupName>
  
  eksctl drain nodegroup --cluster=<clusterName> --name=<nodegroupName> --undo
  
 # To update control plane to the next available version run:
 
 eksctl update cluster --name=<clusterName>
  
  # To create a new nodegroup:
  
  eksctl create nodegroup --cluster=<clusterName> --name=<newNodeGroupName>
  
  # To delete old nodegroup:
  
  eksctl delete nodegroup --cluster=<clusterName> --name=<oldNodeGroupName>
  
  # Updating default add-ons
  
     To update kube-proxy, run:
      
      eksctl utils update-kube-proxy
      
      To update aws-node, run:
      
      eksctl utils update-aws-node
      
      To update corends, run:
      
      eksctl utils update-coredns
      
  # Enable Auto Scaling
  
  eksctl create cluster --asg-access
  
  # Get all identity mappings:
  
  eksctl get iamidentitymapping --name my-cluster-1
  
  # Get all identity mappings matching a role:
  
  eksctl get iamidentitymapping --name my-cluster-1 --role arn:aws:iam::123456:role/testing-role
  
  # Create an identity mapping:
  
   eksctl create iamidentitymapping --name  my-cluster-1 --role arn:aws:iam::123456:role/testing --group system:masters --username admin
  
  # Delete a mapping:
  
  eksctl delete iamidentitymapping --name  my-cluster-1 --role arn:aws:iam::123456:role/testing
