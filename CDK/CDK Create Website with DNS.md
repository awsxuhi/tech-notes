S3 bucket有两种工作模式：Static Web Hosting (Enabled/Disabled)。前者的对象都有一个http的地址，可以直接通过互联网访问；后者无法通过http访问，只有一个具体的S3的endpoint。如果要让Cloudfront通过OAI或者OAC来实现S3只能被CloudFront访问，那么就必须让S3工作在Static Web Hosting Disabled的状态下。这里有两个总结：

在创建Bucket的时候，如果你加了下面两行中的任意一行，就算你随后代码中使用S3Orign来创建这个bucket的Origin，AWS还是会把这个bucket默认变成Static Web Hosting Enabled，Origin也变成了HttpOrigin。这种情况下OAI或者OAC失效，他们的bucket policy不起作用。

```javascript
websiteIndexDocument: 'index.html',
websiteErrorDocument: 'index.html',
```



```javascript
private createWebsiteS3Bucket() {
    const bucketName = this.bucketNamePrefix + '-' + this.props.formattedDate + '-' + this.props.stackIdSuffix;
    const logicalId = 'websiteBucket';
    this.websiteS3Bucket = new Bucket(this, logicalId, {
      bucketName: bucketName,
      publicReadAccess: false,
      blockPublicAccess: BlockPublicAccess.BLOCK_ALL,
      encryption: BucketEncryption.S3_MANAGED, // UNENCRYPTED has the same effect as S3_MANAGED. 
      removalPolicy: RemovalPolicy.DESTROY,
      autoDeleteObjects: true,  // For sample purposes only, if you create an S3 bucket then populate it, stack destruction fails.  This setting will enable full cleanup of the demo.
      // Below 2 lines should be removed, otherwise the Static Web Hosting of the bucket will be automatically enabled which prevents OAI from working.
      // https://github.com/aws/aws-cdk/issues/17153; bucket website endpoint which doesn't allow adding OAI or OAC.
      // websiteIndexDocument: 'index.html',
      // websiteErrorDocument: 'index.html',
    });
  }
```



1. 如果使用Distribution这个construct来创建Cloudfront Distribution，那么此时网页访问不了，报错403。
2. 如果使用CloudFrontWebDistribution这个construct来创建Cloudfront Distribution，那么此时网页可以正常访问。



结论：我们未来会使用Distribution这个功能更强的Construct，所以一定要避免误把Bucket变成静态网站托管模式。https://github.com/aws/aws-cdk/issues/17153 这个地址说明了具体问题。



OAI生成的bucket policy如下：

```json
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity E38M9GK1H3EXIE"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::palette-frontend-2023-03-18-0eddab7a8b45/*"
        },

```



OAC生成的bucket policy如下：

```json
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudfront.amazonaws.com"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::palette-frontend-2023-03-18-0eddab7a8b45/*",
            "Condition": {
                "StringEquals": {
                    "AWS:SourceArn": "arn:aws:cloudfront::581725073534:distribution/E20FRM4AHPPTR3"
                }
            }
        }
```

