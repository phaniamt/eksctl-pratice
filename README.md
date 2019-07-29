# eksctl-pratice

eksctl create cluster -f eks-cluster-create.yaml


# To list the details about  all of the nodegroups, use:

eksctl get nodegroup --cluster=basic-cluster

# To list the details about a nodegroup , use:

eksctl get nodegroup --cluster=basic-cluster --name=kube-cluster-ng-1
