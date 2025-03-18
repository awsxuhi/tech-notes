## 使用cdk代码创建table时候对primary key, GSI的处理方式

问题1: 如果主键由分区键和排序键组成，您需要在创建表时添加排序键。以下是如何修改代码的示例：

```
typescriptCopy code
private createTable() {
  this.table = new Table(this.scope, `dynamodb-${this.props.tableName}`, {
    partitionKey: {
      name: this.props.primaryKey,
      type: AttributeType.STRING
    },
    sortKey: { // Add this line
      name: this.props.sortKey,
      type: AttributeType.STRING
    },
    tableName: this.props.tableName,
    billingMode: BillingMode.PAY_PER_REQUEST,
    removalPolicy: RemovalPolicy.DESTROY,
  });
  this.tableArn = this.table.tableArn;
}
```

问题2: 如果主键只有分区键而没有排序键，代码无需修改。您已经在给定的代码中定义了分区键。

问题3: 如果您要创建一个具有分区键的全局二级索引（GSI），但没有排序键，请参考以下示例：

```
typescriptCopy code
private createTable() {
  this.table = new Table(this.scope, `dynamodb-${this.props.tableName}`, {
    partitionKey: {
      name: this.props.primaryKey,
      type: AttributeType.STRING
    },
    tableName: this.props.tableName,
    billingMode: BillingMode.PAY_PER_REQUEST,
    removalPolicy: RemovalPolicy.DESTROY,
  });

  // Add a GSI with a partition key
  this.table.addGlobalSecondaryIndex({
    indexName: 'yourGSIName',
    partitionKey: {
      name: 'yourGSIPartitionKeyName',
      type: AttributeType.STRING
    },
  });

  this.tableArn = this.table.tableArn;
}
```

问题4: 如果您要创建一个由分区键和排序键组成的全局二级索引（GSI），请参考以下示例：

```
typescriptCopy code
private createTable() {
  this.table = new Table(this.scope, `dynamodb-${this.props.tableName}`, {
    partitionKey: {
      name: this.props.primaryKey,
      type: AttributeType.STRING
    },
    tableName: this.props.tableName,
    billingMode: BillingMode.PAY_PER_REQUEST,
    removalPolicy: RemovalPolicy.DESTROY,
  });

  // Add a GSI with a partition key and a sort key
  this.table.addGlobalSecondaryIndex({
    indexName: 'yourGSIName',
    partitionKey: {
      name: 'yourGSIPartitionKeyName',
      type: AttributeType.STRING
    },
    sortKey: { // Add this line
      name: 'yourGSISortKeyName',
      type: AttributeType.STRING
    },
  });

  this.tableArn = this.table.tableArn;
}
```

请根据您的需求自定义 GSI 名称以及分区键和排序键的名称。



`ReturnValues` 参数用于指定在更新 DynamoDB 表项后返回哪些数据。`ALL_NEW` 意味着返回更新后的完整项，包括所有属性的当前值，而 `UPDATED_NEW` 只返回更新后的属性值。换句话说，如果使用 `ALL_NEW`，则返回整个更新后的项，而使用 `UPDATED_NEW`，则只返回更新的属性值。你可以根据需要选择其中一个选项。



```javascript

// ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
async function queryWithPrimaryPartition(queryParams: APIGatewayProxyEventQueryStringParameters) {
	const KeyValue = queryParams[PRIMARY_KEY!];
	const queryResponse = await ddbClient.query({
		TableName: TABLE_NAME!,
		KeyConditionExpression: '#primaryKey = :KeyValue',
		ExpressionAttributeNames: {
			'#primaryKey': PRIMARY_KEY!
		},
		ExpressionAttributeValues: {
			':KeyValue': KeyValue
		}
	}).promise();
	return JSON.stringify(queryResponse.Items);
}

// ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
async function queryWithGlobalSecondaryIndex(queryParams: APIGatewayProxyEventQueryStringParameters) {
  const gsiIndexName = queryParams.gsiIndexName;
  const gsiKey = queryParams.gsiKey;
  const gsiValue = queryParams.gsiValue;

  const queryResponse = await ddbClient.query({
    TableName: TABLE_NAME!,
    IndexName: gsiIndexName,
    KeyConditionExpression: `#${gsiKey} = :gsiValue`,
    ExpressionAttributeNames: {
      [`#${gsiKey!}`]: gsiKey!,
    },
    ExpressionAttributeValues: {
      ':gsiValue': gsiValue,
    },
  }).promise();

  return JSON.stringify(queryResponse.Items);
}

// ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
async function scanTable(){
    const queryResponse = await ddbClient.scan({
        TableName: TABLE_NAME!
    }).promise();
    return JSON.stringify(queryResponse.Items)
}
```



## Dynamodb的备份和恢复

要实现在 AWS DynamoDB 中备份和恢复数据，您可以使用 AWS 管理控制台、AWS CLI 或 AWS SDK。以下是使用 AWS CLI 的步骤。

**前提条件：**

- 已安装并配置 [AWS CLI](https://aws.amazon.com/cli/)
- 您的 AWS 账户有足够的权限来创建、读取和还原 DynamoDB 备份。

**1. 使用 AWS CLI 创建 DynamoDB 表备份：**

使用以下命令创建一个名为 `solutions` 的表备份。此命令将返回一个备份的 Amazon 资源名称（ARN），您需要在下一步中使用它。

```
cssCopy code
aws dynamodb create-backup --table-name solutions --backup-name solutions-backup
```

请注意备份的 ARN，因为您需要在恢复备份时使用它。

**2. 使用 AWS CLI 还原 DynamoDB 表备份：**

在删除原始表并重新使用 CDK 创建一个空的 `solutions` 表之后，使用以下命令将备份还原到新表：

```
cssCopy code
aws dynamodb restore-table-from-backup --target-table-name solutions --backup-arn <your_backup_arn>
```

其中 `<your_backup_arn>` 是从第 1 步中获得的备份 ARN。

现在，AWS DynamoDB 将从备份中还原数据到新表中。这可能需要一些时间，具体取决于数据量和表结构。

在恢复过程完成后，您可以继续使用新的 `solutions` 表。

如果您更喜欢使用 AWS 管理控制台或 AWS SDK，请参阅 [AWS 文档](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BackupRestore.html)，了解有关备份和还原 DynamoDB 表的详细信息。



## 通过delete来删除1个或者多个item的前端页面的写法

针对后端的deletSingle.ts，前端应该这么写：

```javascript
import axios from 'axios';

const deleteItem = async (partitionKey, sortKey) => {
  const queryParams = {
    [PRIMARY_PARTITION_KEY]: partitionKey,
  };

  if (PRIMARY_SORT_KEY) {
    queryParams[PRIMARY_SORT_KEY] = sortKey;
  }

  try {
    const response = await axios.delete('https://your-api-endpoint.com/your-path', { params: queryParams });
    console.log('删除成功', response.data);
  } catch (error) {
    console.error('删除失败', error);
  }
};

deleteItem('partition_key_value', 'sort_key_value');

```

针对后端可以同时删除多个item的Delete.ts，前端对应的应该这么写：

```javascript
import axios from 'axios';

const deleteItems = async (items) => {
  try {
    const response = await axios({
      method: 'delete',
      url: 'https://your-api-endpoint.com/your-path',
      data: items,
    });
    console.log('删除成功', response.data);
  } catch (error) {
    console.error('删除失败', error);
  }
};

const itemsToDelete = [
  { [PRIMARY_PARTITION_KEY]: 'partition_key_value1', [PRIMARY_SORT_KEY]: 'sort_key_value1' },
  { [PRIMARY_PARTITION_KEY]: 'partition_key_value2', [PRIMARY_SORT_KEY]: 'sort_key_value2' },
  // ...
];

deleteItems(itemsToDelete);

```



虽然PATCH、PUT和POST都用于更新资源，但它们之间确实存在一些重要的区别。以下是它们之间的主要区别：

- PUT方法：PUT方法用于完全替换资源的情况，也就是说，客户端需要提供完整的资源表示形式来替换服务器上的资源。如果客户端没有提供某些属性，则服务器应该将这些属性设置为空值或默认值。PUT方法是幂等的，也就是说，对于相同的资源，多次执行PUT方法应该具有相同的效果。
- PATCH方法：PATCH方法用于部分更新资源的情况，也就是说，客户端只需要提供要更新的属性及其新值，而不需要提供所有的属性。PATCH方法是不幂等的，也就是说，对于相同的资源，多次执行PATCH方法可能会产生不同的结果。
- POST方法：POST方法用于创建新的资源或执行特定的操作，例如提交表单或进行搜索。POST方法通常是不幂等的，也就是说，多次执行POST方法可能会创建多个相同的资源或执行多次相同的操作。



## palette项目中的API定义

#### DataProvider.getSolutions()的用法

```javascript
# get all solutions

import React, { useState, useEffect } from 'react';
import { DataProvider } from './DataProvider';

const dataProvider = new DataProvider();

const YourComponent = () => {
  const [solutions, setSolutions] = useState([]);

  useEffect(() => {
    dataProvider.getSolutions()
      .then(data => {
        setSolutions(data);
      })
      .catch(error => {
        console.error('Error:', error.message);
      });
  }, []);

  return (
    <div>
      {/* 在这里渲染您的解决方案 */}
      {solutions.map(solution => (
        <div key={solution.id}>
          {/* 在这里渲染每个解决方案的详细信息 */}
          <p>{solution.name}</p>
        </div>
      ))}
    </div>
  );
}

export default YourComponent;

```

```javascript
# get one specific item
import { DataProvider } from './DataProvider';

const dataProvider = new DataProvider();

// 根据需要修改查询参数
const query = {
  solutionNameAbbr: 'exampleAbbr',
  // startDate: '2022-01-01',
  // endDate: '2022-12-31',
};

dataProvider.getSolutions(query)
  .then(data => {
    console.log('Solutions:', data);
  })
  .catch(error => {
    console.error('Error:', error.message);
  });

```



#### DataProvider.createSolution的用法

```javascript
import React, { useState } from 'react';
import { DataProvider } from './DataProvider';

const dataProvider = new DataProvider();

const CreateSolutionForm = () => {
  const [name, setName] = useState('');
  const [description, setDescription] = useState('');

  const handleSubmit = async (event: React.FormEvent) => {
    event.preventDefault();

    const newSolution = {
      name: name,
      description: description,
      // 在此处添加其他属性
    };

    try {
      await dataProvider.createSolution(newSolution);
      alert('Solution created successfully!');
    } catch (error) {
      console.error('Error:', error.message);
      alert('Failed to create solution. Please try again.');
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Name:
        <input
          type="text"
          value={name}
          onChange={(event) => setName(event.target.value)}
          required
        />
      </label>
      <br />
      <label>
        Description:
        <textarea
          value={description}
          onChange={(event) => setDescription(event.target.value)}
          required
        />
      </label>
      <br />
      <button type="submit">Create Solution</button>
    </form>
  );
};

export default CreateSolutionForm;

```

