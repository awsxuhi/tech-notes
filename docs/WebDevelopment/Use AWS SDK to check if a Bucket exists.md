下面的这部分代码原本是希望用来在创建一个bucket之前先检查一下这个bucket是不是已经存在，如果存在就不需要取建一个新的。这段代码如果放在CDK里面是可以工作的，但是如果cdk代码里面有其他的组件和这个bucket有依赖，例如在前面创建一个CloudFront Distribution，这个时候就不行了。因为检查bucket是否存在是个异步操作，会造成在创建CloudFront Distribution的时候，bucket还不存在。我还是把这段代码放在这里，未来在其他非CDK场景中可以使用这个来检查bucket是否存在。

提醒：使用bucket.fromBucketname这样的方法来检查bucket是否存在是会失败的。因为永远会返回一个对象包含各种信息。





```typescript
private async createWebsiteStoreS3Bucket_afterCheckingIfBucketExists() {
    const bucketName = webConfig.websiteStoreS3Bucket;
    const logicalId = 'websiteStoreBucket';
    // Check if the bucket already exists
    // The type of the existingBucket variable is IBucket, not Bucket. IBucket is a subset of the Bucket type, 
    // so it lacks some Bucket type properties. We use type conversion here with "as Bucket".
    let existingBucket: Bucket | undefined;

    const checkBucketExists = async (bucketName: string) => {
      const client = new S3Client({});
      const options = { Bucket: bucketName };

      try {
        await client.send(new HeadBucketCommand(options));
        return true;
      } catch (error) {
        if ((error as CustomError).$metadata.httpStatusCode === 404) {
          return false;
        }
        throw error;
      }
    };

    const bucketExists = await checkBucketExists(bucketName);

    if (bucketExists) {
      // The bucket already exists, use the existing bucket
      console.log('Using existing bucket:', bucketName);
      this.websiteStoreS3Bucket = Bucket.fromBucketName(this, logicalId, bucketName) as Bucket;
    } else {
      // The bucket does not exist, create a new one
      console.log('Creating a new bucket...');
      this.websiteStoreS3Bucket = new Bucket(this, logicalId, {
        bucketName,
        publicReadAccess: false,
        blockPublicAccess: BlockPublicAccess.BLOCK_ALL,
        encryption: BucketEncryption.S3_MANAGED,
        removalPolicy: RemovalPolicy.RETAIN,
        transferAcceleration: true,
        cors: [
          {
            allowedHeaders: ['*'],
            allowedMethods: [HttpMethods.GET, HttpMethods.PUT, HttpMethods.POST, HttpMethods.DELETE, HttpMethods.HEAD],
            allowedOrigins: ['*'],
            exposedHeaders: ['ETag'],
          }
        ]
      });
    }
  }
```

