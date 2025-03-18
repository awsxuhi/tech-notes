# K8S: KOPS to build K8S cluster

[TOC]



After setting the DNS name. `k8s.xuhi.xyz` will be used for the cluster. S3 bucket `s3://kops.k8s.xuhi.xyz` has also been created to store states of the configuration.

## Install KOPS in Linux

In Linux, from github:

```shell
curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

chmod +x ./kops
sudo mv ./kops /usr/local/bin/
```



## Install kubectl in Linux

```shell
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```



## Install AWS CLI Tools

### 1st step: install pip

```shell
curl -LO  https://bootstrap.pypa.io/get-pip.py
python get-pip.py
pip -V     #Check the version of PIP
```



### 2nd step: use pip to install AWS CLI

```shell
pip install awscli
aws --version
```



### 3rd step: configure AWS

Regions' name could be found at https://docs.aws.amazon.com/zh_cn/general/latest/gr/rande.html

Run `aws configure` to configure accesskey/secret key.

![image-20190803120105146](/Users/xuhi/Library/Application%20Support/typora-user-images/image-20190803120105146.png)



## Create IAM user kops

The `kops` user will require the following IAM permissions to function properly:

```
AmazonEC2FullAccess
AmazonRoute53FullAccess
AmazonS3FullAccess
IAMFullAccess
AmazonVPCFullAccess
```



## Configure DNS k8s.xuhi.xyz

Create host zone *k8s.xuhi.xyz*,  record the 4 lines about NS record. 

In host zone *xuhi.xyz*, add a NS record `k8s.xuhi.xyz` with the value recorded in the previous step.

You should now able to dig your domain (or subdomain) and see the AWS Name Servers on the other end.

```
dig ns subdomain.example.com
```

Should return something similar to:

```
;; ANSWER SECTION:
subdomain.example.com.        172800  IN  NS  ns-1.awsdns-1.net.
subdomain.example.com.        172800  IN  NS  ns-2.awsdns-2.org.
subdomain.example.com.        172800  IN  NS  ns-3.awsdns-3.com.
subdomain.example.com.        172800  IN  NS  ns-4.awsdns-4.co.uk.
```

This is a critical component of setting up clusters. If you are experiencing problems with the Kubernetes API not coming up, chances are something is wrong with the cluster's DNS.



## Create a S3 bucket to store the state of your cluster

You can manually create a bucket or use below CLI: 

```shell
aws s3api create-bucket --bucket kops.k8s.xuhi.xyz --region ap-northeast-1 --create-bucket-configuration LocationConstraint=ap-northeast-1
```



## Creating your first cluster with KOPS

### Global region: CLI without Environment Variables

Run Kops with multiple parameters.

```shell
kops create cluster --name=k8s.xuhi.xyz --state=s3://kops.k8s.xuhi.xyz --zones=ap-northeast-1a --node-count=2 --node-size=t2.medium --master-size=t2.medium --dns-zone=k8s.xuhi.xyz
```

If you encounter any errors, run below command to delete the cluster. This won't delete the files in S3 bucket. It only delete running cluster.

```bash
kops delete cluster --name=k8s.xuhi.xyz --state=s3://kops.k8s.xuhi.xyz --yes
```

To build the cluster with S3 files, run `kops update cluster`. However, you need to generate key pair with *ssh-keygen* first.

```shell
ssh-keygen -f .ssh/id_rsa
kops create secret --name k8s.xuhi.xyz sshpublickey admin -i ~/.ssh/id_rsa.pub --state=s3://kops.k8s.xuhi.xyz

kops update cluster k8s.xuhi.xyz --state=s3://kops.k8s.xuhi.xyz --yes
```

It takes time for the instances to boot. You may run below commands to validate the cluster.

```shell
kops validate cluster --state=s3://kops.k8s.xuhi.xyz

kubectl get nodes
```

In regions with 2 Availability Zones, deploy the 3 masters in one zone and the nodes can be distributed between the 2 zones. This can be done by specifying the flags:

```
     --master-count=3
     --master-zones=$MASTER_ZONE
     --zones=$NODE_ZONES
```

### Global region: Alternative with Environment Variables

#### Prepare local environment

We're ready to start creating our first cluster! Let's first set up a few environment variables to make this process easier.

```
export NAME=k8s.xuhi.xyz
export KOPS_STATE_STORE=s3://kops.k8s.xuhi.xyz
```

#### Create cluster configuration

Below is a create cluster command. We'll use the most basic example possible, with more verbose examples in [high availability](https://github.com/kubernetes/kops/blob/master/docs/high_availability.md#advanced-example). The below command will generate a cluster configuration, but not start building it. Make sure that you have **generated SSH key pair** before creating the cluster.

```shell
ssh-keygen -f .ssh/id_rsa
```

You will get errors below if you are in China region to run the `kops create` because the accesskey and secretkey are in China region, it cannot access Route53 of global region.

```config
[root@ip-172-31-23-255 ~]# kops create cluster --zones cn-north-1a ${NAME}
I0803 16:09:59.735901   21311 create_cluster.go:519] Inferred --cloud=aws from zone "cn-north-1a"
I0803 16:09:59.796113   21311 subnets.go:184] Assigned CIDR 172.20.32.0/19 to subnet cn-north-1a

error determining default DNS zone: error querying zones: InvalidClientTokenId: The security token included in the request is invalid.
	status code: 403, request id: 5c1b060b-bbca-40c3-910f-43dfebd7cab5
[root@ip-172-31-23-255 ~]#
```

#### Change Node's OS by using different AMI

Default OS is debian. You can use `--image=ami-04e12bea2c2c42a10` to choose different OS AMI.

```shell
kops create cluster \
    --name=${NAME} \
    --image=ami-04e12bea2c2c42a10 \
    --zones=ap-northeast-1c \
    --master-count=1 \
    --master-size=t2.medium \
    --node-count=1 \
    --node-size=t2.medium  \
    --networking=calico \
    --kubernetes-version=1.11.5 \
    --ssh-public-key=~/.ssh/id_rsa.pub \
    --associate-public-ip=true \
    --api-loadbalancer-type=internal \
    --topology=private
```

#### Customize cluster configuration

Now we have a cluster configuration, we can look at every aspect that defines our cluster by editing the description.

```shell
kops edit cluster ${NAME}
```

This opens your editor (as defined by $EDITOR) and allows you to edit the configuration. The configuration is loaded from the S3 bucket we created earlier, and automatically updated when we save and exit the editor.

#### Build the cluster

Now we take the final step of actually building the cluster. This'll take a while. Once it finishes you'll have to wait longer while the booted instances finish downloading Kubernetes components and reach a "ready" state.

```
kops update cluster ${NAME} --yes
```

#### Use the cluster

Remember when you installed `kubectl` earlier? The configuration for your cluster was automatically generated and written to `~/.kube/config` for you!

A simple Kubernetes API call can be used to check if the API is online and listening. Let's use `kubectl` to check the nodes.

```
kubectl get nodes
```

You will see a list of nodes that should match the `--zones` flag defined earlier. This is a great sign that your Kubernetes cluster is online and working.

Also `kops` ships with a handy validation tool that can be ran to ensure your cluster is working as expected.

```
kops validate cluster
```

You can look at all the system components with the following command.

```
kubectl -n kube-system get po
```



### China region: Build cluster in BJS

Below is a create cluster command. We'll use the most basic example possible, with more verbose examples in [high availability](https://github.com/kubernetes/kops/blob/master/docs/high_availability.md#advanced-example). The below command will generate a cluster configuration, but not start building it. Make sure that you have **generated SSH key pair** before creating the cluster.

Note: If you are using Kops 1.6.2 or later, then DNS configuration is optional. Instead, a gossip-based cluster can be easily created. The only requirement to trigger this is to have the cluster name end with `.k8s.local`.  **In China regions, you should use gossip-based cluster.**

- cn-north-1 ami-076828ef599fb3764  Debian

- cn-northwest-1 ami-068b32c3754324d44f  Debian

```shell
export NAME=bjs.k8s.local    
export KOPS_STATE_STORE=s3://kops.cnk8s.xuhi.xyz #The bucket name can be any name.

ssh-keygen -f .ssh/id_rsa   #Make sure you have Keypairs before create cluster
# you can run "ssh-keygen", "-f .ssh/id_rsa" is not needed.

kops create cluster --name=bjs.k8s.local --state=s3://kops.cnk8s.xuhi.xyz --zones=cn-north-1a,cn-north-1b --master-zones cn-north-1b --node-count=2 --master-count=1 --image=ami-076828ef599fb3764 --node-size=t2.medium --master-size=t2.medium --dns-zone=bjs.k8s.local

```

Edit cluster with `kops edit cluster ${NAME}`, and paste below lines in `spec` section.

```yaml
  assets:
    containerRegistry: 937788672844.dkr.ecr.cn-north-1.amazonaws.com.cn
    fileRepository: https://s3.cn-north-1.amazonaws.com.cn/kops-bjs/fileRepository/
  docker:
    logDriver: ""
    registryMirrors:
        - https://registry.docker-cn.com
```

Now build the cluster and validate it. Remember when you installed `kubectl` earlier? The configuration for your cluster was automatically generated and written to `~/.kube/config` for you!

A simple Kubernetes API call can be used to check if the API is online and listening. Let's use `kubectl` to check the nodes.

```shell
kops update cluster ${NAME} --yes

kops validate cluster
kubectl -n kube-system get po
kubectl -n kube-system get nodes
```

### China region: Build cluster in ZHY

Below is a create cluster command. We'll use the most basic example possible, with more verbose examples in [high availability](https://github.com/kubernetes/kops/blob/master/docs/high_availability.md#advanced-example). The below command will generate a cluster configuration, but not start building it. Make sure that you have **generated SSH key pair** before creating the cluster.

Note: If you are using Kops 1.6.2 or later, then DNS configuration is optional. Instead, a gossip-based cluster can be easily created. The only requirement to trigger this is to have the cluster name end with `.k8s.local`.  **In China regions, you should use gossip-based cluster.**

- cn-north-1 ami-076828ef599fb3764  Debian

- cn-northwest-1 ami-068b32c3754324d44f  Debian

```shell
export NAME=zhy.k8s.local    
export KOPS_STATE_STORE=s3://kops.cnk8s.xuhi.xyz

ssh-keygen -f .ssh/id_rsa   #Make sure you have Keypairs before create cluster

kops create cluster --name=zhy.k8s.local --state=s3://kops.cnk8s.xuhi.xyz --zones=cn-northwest-1a,cn-northwest-1b,cn-northwest-1c --master-zones cn-northwest-1b --node-count=3 --master-count=3 --image=ami-068b32c3754324d44 --node-size=t2.medium --master-size=t2.medium --dns-zone=zhy.k8s.local

```

Edit cluster with `kops edit cluster ${NAME}`, and paste below lines in `spec` section.

```yaml
  assets:
    containerRegistry: 937788672844.dkr.ecr.cn-north-1.amazonaws.com.cn
    fileRepository: https://s3.cn-north-1.amazonaws.com.cn/kops-bjs/fileRepository/
  docker:
    logDriver: ""
    registryMirrors:
        - https://registry.docker-cn.com
```

Now build the cluster and validate it. Remember when you installed `kubectl` earlier? The configuration for your cluster was automatically generated and written to `~/.kube/config` for you!

A simple Kubernetes API call can be used to check if the API is online and listening. Let's use `kubectl` to check the nodes.

```shell
kops update cluster ${NAME} --yes

kops validate cluster
kubectl -n kube-system get po
kubectl -n kube-system get nodes
```

Switching context to BJS if you installed clusters in both regions:

```shell
kubectl config use-context bjs.k8s.local
```



## Delete the Cluster

Running a Kubernetes cluster within AWS obviously costs money, and so you may want to delete your cluster if you are finished running experiments.

You can preview all of the AWS resources that will be destroyed when the cluster is deleted by issuing the following command.

```shell
kops delete cluster --name ${NAME}

```

When you are sure you want to delete your cluster, issue the delete command with the `--yes` flag. Note that this command is very destructive, and will delete your cluster and everything contained within it!

```shell
kops delete cluster --name ${NAME} --yes

# If we are in JP region, then can run:
kops delete cluster --name=k8s.xuhi.xyz --state=s3://kops.k8s.xuhi.xyz --yes
```



## Play with your K8S cluster

Next let's try to use the k8s cluster and run a nginx pod/service.

`vi pod_nginx.yml` and put below lines inside:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - name: nginx-port
      containerPort: 80
```

then,

```shell
kubectl create -f pod_nginx.yml
kubectl get pods -o wide

kubectl expose pod nginx-pod --type NodePort
kubectl get svc
# Suppose the port mapping is 80:32549, you need to go to the master node, change security group to allow TCP at 32549 from anywhere. Suppose the master's IP is 54.222.166.51
curl 54.222.166.51:32549

# Delete the service created just now.
kubectl delete svc nginx-pod 
# Create a ELB in AWS to distribute traffic to Nginx Pod
kubectl expose pod nginx-pod --type=LoadBalancer 
kubectl get svc
# Go to AWS management console, copy the DNS name of ELB and past to Chome browser.
curl http://a23be0b9bb66c11e9a8b606ff31a7608-227038507.cn-northwest-1.elb.amazonaws.com.cn/

ssh -i ./.ssh/id_rsa admin@54.222.166.51 # connect to the master api server
```



![image-20190804100418064](/Users/xuhi/Library/Application Support/typora-user-images/image-20190804100418064.png)

## Other Useful Links

[Good doc to show how to install K8S step by step](https://www.itmangoto.cn/2018/12/19/aws-kops-kubernetes-issue/#ssh)

[Github kubernetes/kops documentation](https://github.com/kubernetes/kops/blob/master/docs/aws.md)



