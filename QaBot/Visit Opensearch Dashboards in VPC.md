## Visit Opensearch Dashboards in VPC

Ref: https://github.com/aws-samples/opensearch-vpc-cdk

#### Step 1: Deploy Opensearch domain and Bastion server in VPC

First we need to deploy opensearch domain/cluster in VPC.

Create VPC with the following code:

```typescript
this.vpc = new _ec2.Vpc(this, projectConfig.projectName + "-VPC", {
      subnetConfiguration: [
        {
          name: "public",
          subnetType: _ec2.SubnetType.PUBLIC,
        },
        {
          name: "private",
          subnetType: _ec2.SubnetType.PRIVATE_WITH_EGRESS,
        },
        {
          name: "isolated",
          subnetType: _ec2.SubnetType.PRIVATE_ISOLATED,
        },
      ],
    });
```

Create Opensearch domain in VPC:

```typescript
this.domain = new _opensearchservice.Domain(this, "OpenSearchDomain", {
	   vpc: this.vpc,
     vpcSubnets: [{ subnetType: _ec2.SubnetType.PRIVATE_WITH_EGRESS }],
     securityGroups: [this.opensearchSecurityGroup],
});
```

Create the bastion server:

```typescript
new _ec2.BastionHostLinux(this, "BastionHost", {
  vpc: this.vpc,
  instanceType: _ec2.InstanceType.of(_ec2.InstanceClass.T3, _ec2.InstanceSize.MICRO), // Without this line, T3.Nano will be used.
  securityGroup: this.bastionSecurityGroup,
  machineImage: _ec2.MachineImage.latestAmazonLinux2023(),
  blockDevices: [
    {
      deviceName: "/dev/xvda",
      volume: _ec2.BlockDeviceVolume.ebs(10, {
        encrypted: true,
      }),
    },
  ],
});

```



#### Step 2: Starting the session

Run the following command to access OpenSearch Dashboards

```shell
aws ssm start-session --target i-0f648ea2427e98834 --document-name AWS-StartPortForwardingSessionToRemoteHost --parameters '{"portNumber":["443"],"localPortNumber":["8157"], "host":["vpc-sagemind-dkzcnxsleqgjosbijmq24brggq.us-east-1.es.amazonaws.com"]}' --profile us
```

Here, because I am deploying opensearch domain with profile us (us-east-1). When I start the session, we need to add `--profile us` too. Otherwise, it will fail and return the message as follows:

```shell
aws ssm start-session --target i-0f648ea2427e98834 --document-name AWS-StartPortForwardingSessionToRemoteHost --parameters '{"portNumber":["443"],"localPortNumber":["8157"], "host":["vpc-sagemind-dkzcnxsleqgjosbijmq24brggq.us-east-1.es.amazonaws.com"]}'

An error occurred (TargetNotConnected) when calling the StartSession operation: i-0f648ea2427e98834 is not connected.
```

You can specify other local port number instead of 8157. For example, you can set it to 8080.



#### Step 3: Accessing Opensearch dashboards

After starting session, access `https://localhost:8157/_dashboards` in your browser. Warning may appear because the domain (`*.<region>.es.amazonaws.com`) in the certificate is different from the domain (`localhost`) you access. Since this does not cause serious problems, continue accessing the site, and you will see OpenSearch Dashboards. It supports you login with master user's username and password.

Be sure to use `https` instead of `http`. If you type  `http://localhost:8157/_dashboards` in your browser, it will show `400 Bad Request, the plain HTTP request was sent to HTTPS port` in the page.



An interesting thing is when you deploy opensearch in VPC, there will be no Index tab between Logs and Tags tabs.

![image-20231004151947493](/Users/xuhi/Library/Application Support/typora-user-images/image-20231004151947493.png)

