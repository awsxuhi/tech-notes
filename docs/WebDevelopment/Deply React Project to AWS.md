There are a couple of options to address it.

### Option 1: Use construct cdk-spa-deploy to deploy React project to AWS

More information about this contruct could be found here: https://constructs.dev/packages/cdk-spa-deploy/v/2.0.0-alpha.1?lang=typescript. Although it says it support CDK v2, you should make some changes to the stack file to make it work.

Step 1: create your own react project

```shell
npx create-react-app palette --template typescript
cd palette
git init
npm install
```

After completing the project, you run the following command to output what we need to deploy to folder "./build". A new folder of "build" was created in the directory palette.

```shell
npm run build
```

Step 2: Initiate CDK project

Suppose we initiate the CDK project inside the palette (we can also create another folder outside palette).

```shell
cd palette
mkdir cdk
cd cdk
cdk init --language typescript
npm install --save cdk-spa-deploy
```

Edit palette/cdk/lib/cdk-stack.ts, be aware of the fold being '../build'

```javascript
import * as cdk from 'aws-cdk-lib';
import { SPADeploy } from 'cdk-spa-deploy';

export class CdkStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    
    new SPADeploy(this, 'cfDeploy')
      .createSiteWithCloudfront({
        indexDoc: 'index.html',
        websiteFolder: '../build'
      });
  }
}
```

Step 3: use CDK to deploy

```shell
cd cdk
cdk deploy
```

It works. And it add Error Pages configuration in the CloudFront distribution so that no errors occur when you use react-router to navigate to other pages instead of the home page. (See https://stackoverflow.com/questions/51218979/react-router-doesnt-work-in-aws-s3-bucket)



![image-20230219091441150](/Users/xuhi/Library/Application Support/typora-user-images/image-20230219091441150.png)



### Option 2: Use construct cdk-spa to deploy React project to AWS

More information about this contruct could be found here:https://constructs.dev/packages/cdk-spa/v/2.0.0?lang=typescript. Although it says it support CDK v2, you should make some changes to the stack file to make it work.

Step 1: create your own react project

```shell
npx create-react-app palette --template typescript
cd palette
git init
npm install
```

After completing the project, you run the following command to output what we need to deploy to folder "./build". A new folder of "build" was created in the directory palette.

```shell
npm run build
```

Step 2: Initiate CDK project

Suppose we initiate the CDK project inside the palette (we can also create another folder outside palette).

```shell
cd palette
mkdir cdk
cd cdk
cdk init --language typescript
npm install
```

Before installing this construct, we should use the exact version of 2.46.0 for "aws-cdk-lib". So make changes to package.json:

![image-20230219094131151](/Users/xuhi/Library/Application Support/typora-user-images/image-20230219094131151.png)

Now run the following line:

```shell
npm install
npm install --save cdk-spa
```



Edit palette/cdk/lib/cdk-stack.ts, be aware of the fold being '../build'. We need to specify "bucketName: 'palettefrontend' " to avoid encounting an error saying the bucket was already existed.

```javascript
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';

// import * as cdk from '@aws-cdk/core';
import { CdkSpa } from 'cdk-spa';

export class CdkNewStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, {
      env: {
        account: "581725073534",
        region: "ap-northeast-1"
      }
    });

    new CdkSpa(this, 'spaDeploy', { 
        encryptBucket: true,
        bucketName: 'palettefrontend'
      })
      .createSiteFromHostedZone({
        zoneName: 'xuhi.solutions.aws.a2z.org.cn',
        subdomain: 'spa',
        indexDoc: 'index.html',
        websiteFolder: '../build'
      });
  }
}
```

Step 3: use CDK to deploy

```shell
cd cdk
cdk deploy
```

It works. And it add Error Pages configuration in the CloudFront distribution so that no errors occur when you use react-router to navigate to other pages instead of the home page. (See https://stackoverflow.com/questions/51218979/react-router-doesnt-work-in-aws-s3-bucket)

![image-20230219113146846](/Users/xuhi/Library/Application Support/typora-user-images/image-20230219113146846.png)

And we found the Bucket itself was not public accessible. We use bucket policy to configure the bucket can only be accessed by CloudFront OAI. The bucket policy automatically applied is as follows. According to CloudFront doc, we are going to use CloudFront OAC to replace the legacy OAI later on.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity E2L59V04VRJ3U4"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::palettefrontend/*"
        }
    ]
}
```

