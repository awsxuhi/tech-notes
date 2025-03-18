# Install AWSCLI on CentOS7

### 1st step: install pip

```shell
curl -LO  https://bootstrap.pypa.io/get-pip.py
python get-pip.py
pip -V     #Check the version of PIP
```



###  2nd step: use pip to install AWS CLI

```shell
pip install awscli
aws --version
```



### 3rd step: configure AWS

Regions' name could be found at https://docs.aws.amazon.com/zh_cn/general/latest/gr/rande.html
