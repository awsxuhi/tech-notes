# Setup Kubernetes Cluster on CentOS 7



In this tutorial, we will use `kubeadm` to configure Kubernetes cluster on CentOS 7.4.

> IMPORTANT NOTE: Ensure swap is disabled on both master and worker nodes. Kubernetes requires swap to be disabled in order for it to successfully configure Kubernetes Cluster.

Before you start setting up Kubernetes cluster, it is recommended that you update your system to ensure all security updates are up-to-date.

Execute below command:

```

```

# Install Docker

In order to configure kubernetes cluster, it is require to install Docker. Execure below command to install Docker on your system.

```

```

Enable & start Docker service.

```

```

Verify docker version is 1.12 and greater.

```


```

# Install Kubernetes packages

Configure yum to install kubeadm, kubectl, and kubelet.

Copy below content and execute on your centos.

```

```

Disable SELinux:

```

```

In order for Kubernetes cluster to communicate internally, we have to disable `SELinux`. Currently SELinux is not fully supported. In future this may change when support for SELinux is improved.

Installing packages for Kubernetes:

#### On Master node:

```

```

Enable & start kubelet service:

```

```

#### On worker nodes:

```

```

Copy below content and excute on centos master and worker nodes. There has been an issue reported that traffic in iptable is been routed incorrectly. Below settings will make sure IPTable is configured correctly.

```

```

Execute below command to apply above changes.

```

```

Once you have completed above steps on all your centos nodes including master & worker nodes. Let's go ahead and configure Master node.

# Disable Firewall on Master node

Kubernets Cluster uses IPTables to manage inbound and outbound traffic. In order to avoid conflict, we will disable firewalld on centos 7 system. If you prefer to keep firewall enabled. I recommend allowing port `6443` to allow communication from worker node to master node.

Disable:

```

```

Stop:

```

```

Check status:

```

```

# Configuring Kubernetes Master node

On your CentOS master node execute below commands:

```

```

> NOTE: Ensure SWAP is disabled on all your Centos system. Kubernetes cluster configuration will fail if swap is not disabled.

Once above command is completed, it will output Kubernetes cluster information. Please make sure that you have the token information somewhere safe. It will be needed to join worker nodes to Kubernetes cluster.

Output from `kubeadm init`:

```text

```

On Master node apply below changes after `kubeadm init` successful configuration:

```

```

## Configuring Pod Networking

Before we setup worker nodes, we need to ensure pod networking is functional. Pod networking is also a dependency for `kube-dns` pod to manage pod dns.

```


```

Ensure all pods are in running status by executing below command:

```

```

It may take sometime depending on your system configuration and network speed. It pulls all the images from online to run required pods in system namespace.

Once all the pods are in running status, let's configure worker nodes.

# Configure Kubernetes Worker nodes

To configure worker nodes to be part of Kubernetes cluster. We need to use `kubeadm join` command with token received from master node.

Execute below command to join worker node to Kubernetes Cluster.

```

```

Once the node has joined the cluster, you will see similar output on your console.

```text

```

On kubernetes master node execute below command to see node status. If you see node status ready, that means your worker node is ready to host pods.

```

```

Output for above command

```text

```

If you see worker node status ready, then you are ready to deploy pods on your worker nodes.