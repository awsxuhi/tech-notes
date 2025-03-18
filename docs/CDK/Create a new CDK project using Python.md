## Create a new CDK project using Python

#### Step 1: check the user you are going to use AWS resources

```shell
aws sts get-caller-identity

{
    "UserId": "AIDAYO4MEGB7GYMIH4SJH",
    "Account": "581725073534",
    "Arn": "arn:aws:iam::581725073534:user/amplify-Xys6G"
}
```

```shell
$ aws sts get-caller-identity --query Account --output text

581725073534
```

```shell
npm --version

8.19.2 
```



#### Step 2: Install CDK

```shell
npm install -g aws-cdk@latest
```

```shell
cdk --version
```



#### Step 3: Bootstrap

```shell
cdk bootstrap
cdk bootstrap "aws://754249199081/us-east-1"
```



#### Step 4: Create new projects

```shell
cdk init --language python
```

