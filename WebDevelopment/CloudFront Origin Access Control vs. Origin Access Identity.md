Doc: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html

Here comes the OAI policy statement that applies to a specific S3 bucket policy.

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

To change the OAI to OAC, we use the following policy as Bucket Policy:

```json
{
        "Version": "2008-10-17",
        "Id": "PolicyForCloudFrontPrivateContent",
        "Statement": [
            {
                "Sid": "AllowCloudFrontServicePrincipal",
                "Effect": "Allow",
                "Principal": {
                    "Service": "cloudfront.amazonaws.com"
                },
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::palettefrontend/*",
                "Condition": {
                    "StringEquals": {
                      "AWS:SourceArn": "arn:aws:cloudfront::581725073534:distribution/E38P6CJCV847NZ"
                    }
                }
            }
        ]
      }
```

