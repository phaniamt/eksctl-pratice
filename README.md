# eksctl-pratice

eksctl create cluster -f eks-cluster-create.yaml


# To list the details about  all of the nodegroups, use:

eksctl get nodegroup --cluster=basic-cluster

# To list the details about a nodegroup , use:

eksctl get nodegroup --cluster=basic-cluster --name=kube-cluster-ng-1

# The nodegroups ng-1-workers and ng-2-builders can be created with this command

eksctl create nodegroup --config-file=eks-node-group-create.yaml

# A nodegroup can be scaled by using the eksctl scale nodegroup command:

eksctl scale nodegroup --cluster=dev-cluster --nodes=3 --name=ng-2-builders
