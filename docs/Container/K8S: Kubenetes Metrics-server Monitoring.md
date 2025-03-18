# Kubenetes: Metrics-server Monitoring

```shell
mkdir githublocal
cd githublocal

git clone https://github.com/kubernetes-incubator/metrics-server.git
cd metrics-server
kubectl create -f deploy/1.8+/

# Wait for a while. Give metrics-server some time to collect and process data.

kubectl top node
kubectl top pod
```



If you install K8S cluster with KOPS default configuration, you will encounter error of

```shell
error: metrics not available yet
```

 In this case, you should do 2 things. One is to modify KOPS config with

```yaml
kops edit cluster --name=k8s.xuhi.xyz --state=s3://kops.k8s.xuhi.xyz

# Now edit the config and add below lines:
kubelet:
  anonymousAuth: false
  authenticationTokenWebhook: true
  authorizationMode: Webhook
  
kops update cluster --name=k8s.xuhi.xyz --state=s3://kops.k8s.xuhi.xyz/ --yes
kops rolling-update cluster --name=k8s.xuhi.xyz --state=s3://kops.k8s.xuhi.xyz/ --yes
# The rolling update will drain master node before worker node.
```

The other thing is to modify metric-server's yaml file like:

```shell
cd metrics-server/deploy/1.8+
vi metrics-server-deployment.yaml

# Add below lines
command:
- /metrics-server
- --metric-resolution=30s
- --kubelet-insecure-tls
- --kubelet-preferred-address types=InternalIP,Hostname,InternalDNS,ExternalDNS,ExternalIP
volumeMounts:

# Redeploy the metrics-server again
kubectl delete -f .
kubectl create -f .
```

For more information, go to [githublink.](https://github.com/kubernetes-incubator/metrics-server/issues/212)



To see logs when you are troubleshooting.

```shell
kubectl get all -n kube-system # Get the pod name of metrics-server
kubectl logs -c metrics-server -n kube-system -f metrics-server-5f7b8f59bb-crhkt
```





# Other links



[How to modify the deployment yaml to make sure it could be used inside China 1](https://www.jianshu.com/p/7a4c97ac0954)

[How to modify the deployment yaml to make sure it could be used inside China 2](https://www.cnblogs.com/tchua/p/10855001.html)

[You need to change kubelet configuration by modify Kops config](https://github.com/kubernetes-incubator/metrics-server/issues/212)