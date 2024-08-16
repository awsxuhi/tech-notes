

# Minikube

## Installation of Minikube

**1st step** is to install *kubectl*. Install kubectl binary with curl on Linux

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl

chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version
```

In order for kubectl to find and access a Kubernetes cluster, it needs a [kubeconfig file](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/), which is created automatically when you create a cluster using `kube-up.sh` or successfully deploy a Minikube cluster. By default, kubectl configuration is located at `~/.kube/config`.

Check that kubectl is properly configured by getting the cluster state:

```shell
kubectl cluster-info
```

If you see a URL response, kubectl is correctly configured to access your cluster.

**2nd step** is optionally to enable shell autocompletion.

kubectl provides autocompletion support for Bash and Zsh, which can save you a lot of typing. Below are the procedures to set up autocompletion for Zsh.

add the following to your `~/.zshrc` file:

```shell
source <(kubectl completion zsh)
```

After reloading your shell, kubectl autocompletion should be working.

If you get an error like `complete:13: command not found: compdef`, then add the following to the beginning of your `~/.zshrc` file:

```shell
autoload -Uz compinit
compinit
```

**To check if virtualization is supported on Linux, run the following command and verify that the output is non-empty. Unfortunately, AWS output of C5, M5 is empty. (see 3rd step)**

`egrep --color 'vmx|svm' /proc/cpuinfo`



**3rd step** is to install virtualbox on CentOS.



![image-20190803103547561](/Users/xuhi/Library/Application Support/typora-user-images/image-20190803103547561.png)

![image-20190803103529574](/Users/xuhi/Library/Application Support/typora-user-images/image-20190803103529574.png)



![image-20190803103706420](/Users/xuhi/Library/Application Support/typora-user-images/image-20190803103706420.png)



## Play with Minikube



The `minikube start` command create a "Kubectl context" called "minikube". This context contains the configuration to communicate with your minikube cluster.



Minikube sets this context to default automatically, but if you need to switch back to it in the future, run: 

`kubectl config use-context minikube`, 

or pass the context on each command like this: `kubectl get pods --context=minikube`.

After installation of minikube, run: `minikube start` to start your k8s environment.

```zsh
kubectl config view
kubectl config get-contexts  #With this you can see the context is minikube.
kubectl cluster-info  #See the Kubernetes master is running on IP...

minikube ssh  #To enter the virtual machine created by minikube
docker version
```



Use `kubectl version` to check both client version and server version. Then create a `pod_nginx.yml` as follows:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
	ports:
	- containerPort: 8000
```



`kubectl create -f pod_nginx.yml`

`kubectl delete -f pod_nginx.yml`





