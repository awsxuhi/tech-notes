# EKS: create cluster



```shell
eksctl create cluster --name xuhai --version 1.14 --nodegroup-name standard-workers --node-type t3.medium  --nodes 3 --nodes-min 1 --nodes-max 4 --node-ami auto -r ap-northeast-2

[ℹ]  using region ap-northeast-2
[ℹ]  setting availability zones to [ap-northeast-2c ap-northeast-2b ap-northeast-2a]
[ℹ]  subnets for ap-northeast-2c - public:192.168.0.0/19 private:192.168.96.0/19
[ℹ]  subnets for ap-northeast-2b - public:192.168.32.0/19 private:192.168.128.0/19
[ℹ]  subnets for ap-northeast-2a - public:192.168.64.0/19 private:192.168.160.0/19
[ℹ]  nodegroup "standard-workers" will use "ami-023bb403131889300" [AmazonLinux2/1.14]
[ℹ]  using Kubernetes version 1.14
[ℹ]  creating EKS cluster "xuhai" in "ap-northeast-2" region
[ℹ]  will create 2 separate CloudFormation stacks for cluster itself and the initial nodegroup
[ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=ap-northeast-2 --name=xuhai'
[ℹ]  CloudWatch logging will not be enabled for cluster "xuhai" in "ap-northeast-2"
[ℹ]  you can enable it with 'eksctl utils update-cluster-logging --region=ap-northeast-2 --name=xuhai'
[ℹ]  2 sequential tasks: { create cluster control plane "xuhai", create nodegroup "standard-workers" }
[ℹ]  building cluster stack "eksctl-xuhai-cluster"
[ℹ]  deploying stack "eksctl-xuhai-cluster"
[ℹ]  building nodegroup stack "eksctl-xuhai-nodegroup-standard-workers"
[ℹ]  deploying stack "eksctl-xuhai-nodegroup-standard-workers"
[✔]  all EKS cluster resource for "xuhai" had been created
[✔]  saved kubeconfig as "/root/.kube/config"
[ℹ]  adding role "arn:aws:iam::581725073534:role/eksctl-xuhai-nodegroup-standard-w-NodeInstanceRole-1UAO3GQ3MW5HN" to auth ConfigMap
[ℹ]  nodegroup "standard-workers" has 0 node(s)
[ℹ]  waiting for at least 1 node(s) to become ready in "standard-workers"
[ℹ]  nodegroup "standard-workers" has 3 node(s)
[ℹ]  node "ip-192-168-2-248.ap-northeast-2.compute.internal" is not ready
[ℹ]  node "ip-192-168-54-214.ap-northeast-2.compute.internal" is ready
[ℹ]  node "ip-192-168-92-156.ap-northeast-2.compute.internal" is not ready
[ℹ]  kubectl command should work with "/root/.kube/config", try 'kubectl get nodes'
[✔]  EKS cluster "xuhai" in "ap-northeast-2" region is ready
```



ServiceAccount:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: s3-echoer
spec:
  template:
    spec:
      serviceAccountName: s3-echoer
      containers:
      - name: main
        image: amazonlinux:2018.03
        command:
        - "sh"
        - "-c"
        - "curl -sL -o /s3-echoer https://github.com/mhausenblas/s3-echoer/releases/latest/download/s3-echoer-linux && chmod +x /s3-echoer && echo This is an in-cluster test | /s3-echoer eksworkshop-xuhai"
        env:
        - name: AWS_DEFAULT_REGION
          value: "us-east-1"
        - name: ENABLE_IRP
          value: "true"
      restartPolicy: Never
```

