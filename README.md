# Ref 
https://eksctl.io/introduction/getting-started/

https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html

https://github.com/weaveworks/eksctl

https://github.com/kubernetes-sigs/aws-ebs-csi-driver

# Get AMI id from termial

    aws ssm get-parameter --name /aws/service/eks/optimized-ami/1.14/amazon-linux-2/recommended/image_id --region eu-central-1 --query Parameter.Value --output text


# Configure aws credentails and region with envs

export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE

export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

export AWS_DEFAULT_REGION=us-east-1

# Create cluster using eksctl

eksctl create cluster -f eks-cluster-create.yaml

    apiVersion: eksctl.io/v1alpha5
    kind: ClusterConfig

    metadata:
      name: phani-cluster
      region: eu-central-1
      tags: {environment: beta}
    availabilityZones: ["eu-central-1a", "eu-central-1b", "eu-central-1c"]
    vpc:
      nat:
        gateway: Disable # other options: HighlyAvailable , Single (default)
    nodeGroups:
      - name: phani-cluster-ng-1
        labels:
          nodegroup-type: backend-workloads
          server: dev
        instanceType: t2.small
        minSize: 1
        maxSize: 2
        desiredCapacity: 1
        availabilityZones: ["eu-central-1a"]
        preBootstrapCommands:
            # Replicate what --enable-docker-bridge does in /etc/eks/bootstrap.sh
            # Enabling the docker bridge network. We have to disable live-restore as it
            # prevents docker from recreating the default bridge network on restart
           - "cp /etc/docker/daemon.json /etc/docker/daemon_backup.json"
           - "echo -e '.bridge=\"docker0\" | .\"live-restore\"=false' >  /etc/docker/jq_script"
           - "jq -f /etc/docker/jq_script /etc/docker/daemon_backup.json | tee /etc/docker/daemon.json"
           - "systemctl restart docker"
    #    ami: auto
        ami: ami-0f64557dd6506a4aa
        volumeSize: 10
        ssh: # use existing EC2 key
          publicKeyName: phani-new
        iam:
          withAddonPolicies:
            imageBuilder: true
            autoScaler: true
            externalDNS: true
            certManager: true
            appMesh: true
            ebs: true
            fsx: true
            efs: true
            albIngress: true
            xRay: true
            cloudWatch: true
      - name: phani-cluster-ng-2
        labels:
          nodegroup-type: frond-workloads
          server: prod
        instanceType: t2.micro
        minSize: 1
        maxSize: 2
        desiredCapacity: 1
        availabilityZones: ["eu-central-1b"]
        preBootstrapCommands:
            # Replicate what --enable-docker-bridge does in /etc/eks/bootstrap.sh
            # Enabling the docker bridge network. We have to disable live-restore as it
            # prevents docker from recreating the default bridge network on restart
           - "cp /etc/docker/daemon.json /etc/docker/daemon_backup.json"
           - "echo -e '.bridge=\"docker0\" | .\"live-restore\"=false' >  /etc/docker/jq_script"
           - "jq -f /etc/docker/jq_script /etc/docker/daemon_backup.json | tee /etc/docker/daemon.json"
           - "systemctl restart docker"
     #    ami: auto
        ami: ami-0f64557dd6506a4aa
        volumeSize: 10
        ssh: # use existing EC2 key
          publicKeyName: phani-new
        iam:
          withAddonPolicies:
            imageBuilder: true
            autoScaler: true
            externalDNS: true
            certManager: true
            appMesh: true
            ebs: true
            fsx: true
            efs: true
            albIngress: true
            xRay: true
            cloudWatch: true


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

kubectl label nodes -l alpha.eksctl.io/nodegroup-name=kube-cluster-ng-1 servers=beta

# Check the labels

kubectl get nodes --show-labels

# To delete a nodegroup, run:

eksctl delete nodegroup --cluster=phani-cluster --name=kube-cluster-ng-1 --region=us-east-1
  
NOTE: this will drain all pods from that nodegroup before the instances are deleted

 # but if you need to drain a nodegroup without deleting it, run:
 
eksctl drain nodegroup --cluster=phani-cluster --name=kube-cluster-ng-1 --region=us-east-1
  
  eksctl drain nodegroup --cluster=phani-cluster --name=kube-cluster-ng-1 --region=us-east-1 --undo
  
 # To update control plane to the next available version run:
 
 eksctl update cluster --name=phani-cluster
  
  # To create a new nodegroup:
  
  eksctl create nodegroup --cluster=phani-cluster --name=newNodeGroupName
  
  # To delete old nodegroup:
  
  eksctl delete nodegroup --cluster=phani-cluster --name=oldNodeGroupName
  
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
  
  # Delete the cluster 
  
  eksctl delete cluster --name=phani-cluster --region=us-east-1
