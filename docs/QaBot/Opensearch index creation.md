





## Opensearch index creation

使用cdk进行部署后，需要采用下面的步骤来进一步配置opensearch的集群使得iam用户有权限来访问dashboard。

第一步，根据secrets manager里面保存的master user的username和password登录到dashboard中；

第二部，选择左上角的Dev Tools，在出现的console里面粘贴下面的代码，并点击代码第一行左上角的三角图标来执行。

```json
PUT _plugins/_security/api/rolesmapping/all_access
{
  "backend_roles": [],
  "hosts": [],
  "users": ["admin", "arn:aws:iam::581725073534:user/xuhai"],
  "and_backend_roles": []
}
```

完成这个操作后，可以到Dev Tools左侧导航找到Security->Roles。此时可以看到IAM user已经被添加，并赋予all_access。

![image-20230908233643200](/Users/xuhi/Library/Application Support/typora-user-images/image-20230908233643200.png)

```json
PUT bedrock-index
{
    "settings": {
      "index": {
        "number_of_shards": "3",
        "knn.algo_param": {
          "ef_search": "32"
        },
        "knn": "true",
        "number_of_replicas": "2",
        "refresh_interval" : "10s"
      }
    },
    "mappings": {
      "properties": {
        "vector_field": {
          "type": "knn_vector",
          "dimension": 4096,
          "method": {
            "engine": "nmslib",
            "space_type": "l2",
            "name": "hnsw",
            "parameters": {
              "ef_construction": 32,
              "m": 8
            }
          }
        }
      }
    }
}


GET bedrock-index/_search
{}


DELETE bedrock-index

```



Cognito authentication只用在登录dashboard吗？master user能用这个来吗？

public和vpc，哪个是最佳实践
