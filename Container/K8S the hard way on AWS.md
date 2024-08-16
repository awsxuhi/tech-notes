# K8S the hard way on AWS

## Install the client tools

```shell
wget https://pkg.cfssl.org/R1.2/cfssl_linux-amd64 
wget https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64

chmod +x cfssl_linux-amd64 cfssljson_linux-amd64
sudo mv cfssl_linux-amd64 /usr/local/bin/cfssl
sudo mv cfssljson_linux-amd64 /usr/local/bin/cfssljson

# Verification
cfssl version
kubectl version --client
```

