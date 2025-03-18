

## CORS

 

在创建apiGateway的时候，无需使用defaultCorsPreflightOptions，我们只需要在未来创建的resources上增加CORS就可以了。而且如果不是最终要访问的resource，例如/test/helloworld里面的test，就不需要enable CORS。只需要在helloworld上enable CORS。



```javascript
    this.api = new RestApi(this, 'PaletteApi', {
      restApiName: 'PaletteApi',
      deployOptions: { stageName: 'api' }, // Change the stageName from prod to api.
      /* 1. Setting binaryMediaTypes will lead to CORS 500 error on OPTIONS Preflight request. Since we only use the api to transfer json object. 
       * We don't need to set binaryMediaTypes: [image/*, application/pdf, application/msword, application/vnd.openxmlformats-officedocument.wordprocessingml.document]
       * 2. As we don't offer any methods (e.g., GET/POST) on the '/', we don't need to set defaultCorsPreflightOptions CORS. 
       */       
      // binaryMediaTypes: ['*/*'],
      // defaultCorsPreflightOptions: {
      //   allowOrigins: Cors.ALL_ORIGINS,
      //   allowMethods: Cors.ALL_METHODS
      // },
    });
```



## binaryMediaTypes

如果希望apiGateway可以同时支持从dynamodb数据库中读取并返回的Json文件，以及一些二进制数据例如图片。就需要设置二进制媒体类型参数binaryMediaTypes。

设置 `binaryMediaTypes: ['*/*']` 会让 API Gateway 将所有请求内容类型视为二进制数据。这包括 JSON 数据、图片、PDF 和 Word 文件等。这样就会造成OPTIONS Method所需要传送的包含allow origin headers的json文件(application/json也被通配符匹配了）无法被正确读取和传输，最终在浏览器里面看到的就是preflight 500错误，并且显示CORS错误。如果你按照CORS去找解决方案就会被困死，因为真正原因不是CORS。（If you have `binaryMediaTypes` enabled then CORS won't be sent.）

解决方案是，不要指定 `binaryMediaTypes: ['*/*']` ，而改成具体的列表，把你需要被支持的二进制文件类型列出来，例如下面这个设置就表示只支持所有图片，PDF和所有office文件。JSON文件还是会以文本传送，这样就不会出错了。

/* 1. Setting binaryMediaTypes will lead to CORS 500 error on OPTIONS Preflight request. Since we only use the api to transfer json object. 

​       \* We don't need to set binaryMediaTypes: [image/*, application/pdf, application/msword, application/vnd.openxmlformats-officedocument.wordprocessingml.document]

​       \* 2. As we don't offer any methods (e.g., GET/POST) on the '/', we don't need to set defaultCorsPreflightOptions CORS. 

​       */       

```javascript
 this.api = new RestApi(this, 'PaletteApi', {
      restApiName: 'PaletteApi',
      deployOptions: { stageName: 'api' }, 
      binaryMediaTypes: [
        'image/*', 
        'audio/*', 
        'video/*', 
        'application/pdf', 
        'application/msword', 
        'application/vnd.*',
        ],
      }
    );
```



具体参考链接如下：

https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-payload-encodings.html

https://cloudnamaste.com/500-internal-server-error-mock-integration/



api gateway是不是需要先检测header，如果是application/json，才使用CONVERT_TO_TEXT？如果是image这样的图片，CONVERT_TO_TEXT后，lambda函数是否会无法正确处理呢？

是的，根据您的需求，API Gateway 可能需要先检查请求头中的 `Content-Type`，然后根据其值决定是否应用 `CONVERT_TO_TEXT`。但是，API Gateway 中的 `contentHandling` 选项只能应用于整个集成，而不能根据 `Content-Type` 动态改变。这意味着在这种情况下，您可能需要为处理 JSON 数据和处理图像数据创建两个不同的 API Gateway 资源和集成。

当将二进制数据转换为文本时，您可能会破坏数据。例如，在处理图像时，使用 `CONVERT_TO_TEXT` 可能会导致损坏的图像。因此，建议您为处理 JSON 数据和处理图像数据创建两个单独的 API Gateway 资源和集成。这样，您可以为 JSON 数据使用 `CONVERT_TO_TEXT`，而为图像数据使用默认的 `CONVERT_TO_BINARY` 设置（或者不设置 `contentHandling`，因为默认行为是透传数据）。

对于 JSON 数据，Lambda 函数应当处理并返回 JSON 格式的数据。而对于图像数据，Lambda 函数应当处理并返回二进制格式的数据。根据您的需求和具体实现，这可能涉及到不同的处理逻辑，所以将这两种类型的请求分开处理是一个好主意。

====

AWS API Gateway和Lambda确实对文件大小有限制。API Gateway的请求和响应的有效载荷大小限制为10 MB，而Lambda的事件有效载荷大小限制为6 MB。对于较大的文件，您可以考虑使用预签名URL直接将文件上传到S3，而不是通过API Gateway和Lambda。



针对较大文件上传，有几种可能的实现方式。这里我们讨论三种主要方法：1）通过API Gateway和Lambda，2）使用预签名URL直接上传到S3，3）使用分块上传到S3。接下来，我们将分析这三种方法的优劣，并确定最佳方案。

1. 通过API Gateway和Lambda： 优势：

- 可以对上传的文件进行验证和处理，增加安全性和灵活性。
- 可以轻松与其他AWS服务集成。

劣势：

- API Gateway和Lambda都有有效载荷大小限制（API Gateway为10 MB，Lambda为6 MB），不适合大文件上传。
- Lambda可能会因为处理大文件而导致执行时间过长，从而增加计算成本。

1. 使用预签名URL直接上传到S3： 优势：

- 绕过API Gateway和Lambda的大小限制，适用于大文件上传。
- 与S3直接交互，降低了延迟和成本。
- 可以设置过期时间，增加安全性。

劣

https://aws.amazon.com/blogs/compute/uploading-large-objects-to-amazon-s3-using-multipart-upload-and-transfer-acceleration/ Uploading large objects to Amazon S3 using multipart upload and transfer acceleration （very good, with git repo)



