## Setup Websocket API

#### Step 1: Install wscat in Mac

In Mac, use the following command to install `wscat`:

```shell
> sudo npm install -g wscat
```

Check if it's in the path:

```shell
> which wscat
/usr/local/bin/wscat
```



#### Step 2: To connect to your API

Ref: https://docs.aws.amazon.com/apigateway/latest/developerguide/websocket-api-chat-app.html

1. Use the following command to connect to your API. When you connect to your API, API Gateway invokes the `$connect` route. When this route is invoked, it calls a Lambda function (Connection Handler) that stores your connection ID in DynamoDB. 

```shell
> wscat -c wss://7a83xkluvf.execute-api.eu-north-1.amazonaws.com/production
Connected (press CTRL+C to quit)
>
```

Now, the DynamoDb connection table shows a new item about the connection ID:

![image-20230928115922042](/Users/xuhi/Library/Application Support/typora-user-images/image-20230928115922042.png)

2. Open a new terminal and run the **wscat** command again with the following parameters.

```shell
> wscat -c wss://7a83xkluvf.execute-api.eu-north-1.amazonaws.com/production
Connected (press CTRL+C to quit)
>
```

This gives you two connected clients that can exchange messages. A new connection Id was created in the DynamoDb table.

![image-20230928120449799](/Users/xuhi/Library/Application Support/typora-user-images/image-20230928120449799.png)

