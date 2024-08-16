CDK to deploy python lambda



#### Step 1: create a directory for a new python project

```shell
mkdir lambda/question-answering
cd lambda/question-answering

pyenv versions
pyenv local 3.10.12
pipenv --python 3.10

pipenv shell
Launching subshell in virtual environment...
>  . /Users/xuhi/.local/share/virtualenvs/qa-bot-3fA1RWZ0/bin/activate

# after finishing writting code
pipenv install requests

pipenv run python main.py
```

check the dependency:

```shell
> pipenv graph
requests==2.31.0
├── certifi [required: >=2017.4.17, installed: 2023.7.22]
├── charset-normalizer [required: >=2,<4, installed: 3.2.0]
├── idna [required: >=2.5,<4, installed: 3.4]
└── urllib3 [required: >=1.21.1,<3, installed: 2.0.4]
```



#### Step 2: create a requirements.txt

1st approach:

```shell
pipenv requirements > requirements.txt    
```

Output:

```shell
-i https://pypi.org/simple
certifi==2023.7.22; python_version >= '3.6'
charset-normalizer==3.2.0; python_full_version >= '3.7.0'
idna==3.4; python_version >= '3.5'
requests==2.31.0
urllib3==2.0.4; python_version >= '3.7'
```

Here, suggest changing "-i https://pypi.org/simple" to "-i https://pypi.tuna.tsinghua.edu.cn/simple". It's to use the mirror site in China. Otherwise, you will sometimes face the error caused by network.

#### Step 3: Install the library into a directory with the name `python`

```shell
pip install -r requirements.txt --target ./lambda_layer/python
```

Here, the name "python" as a directory name is a must.



#### Step 4: configure Function/LambdaIntegration in CDK

```javascript
 /*++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*/
  private addResourceWithCorsAndCognitoAuthorizer() {
    const qaResource = this.api.root.addResource("qa");
    const qaFunction = this.createPythonFunction("question-answering"); // the string is the directory name
    qaResource.addMethod("GET", new _apigateway.LambdaIntegration(qaFunction));
    qaResource.addMethod("POST", new _apigateway.LambdaIntegration(qaFunction));

    const tResource = this.api.root.addResource("t");
    const tFunction = this.createPythonFunction("t");
    tResource.addMethod("GET", new _apigateway.LambdaIntegration(tFunction));
    tResource.addMethod("POST", new _apigateway.LambdaIntegration(tFunction));
  } /* End of addResourceWithCorsAndCognitoAuthorizer */

  /*++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*/
  private createPythonFunction(functionName: string, runtime: _lambda.Runtime = _lambda.Runtime.PYTHON_3_10, timeoutMinutes: number = 5): _lambda.Function {
    return new _lambda.Function(this, `${projectConfig.projectName}-${functionName}`, {
      functionName: `${projectConfig.projectName}-${functionName}`,
      runtime: runtime,
      code: _lambda.Code.fromAsset(join(__dirname, "..", "lambda", functionName)),
      handler: "main.lambda_handler",
      layers: [
        new _lambda.LayerVersion(this, `${projectConfig.projectName}-${functionName}-layer`, {
          // 通过pip install -r requirements.txt --target ./lambda_layer/python来安装依赖的库，必须是python目录，但是在下面的这行代码中，不能有`python`
          code: _lambda.Code.fromAsset(join(__dirname, "..", "lambda", functionName, "lambda_layer")),
          compatibleRuntimes: [runtime],
        }),
      ],
      timeout: Duration.minutes(timeoutMinutes),
    });
  }
```



#### Other conclusions

问题1：根据我的项目的`Pipfile`生成对应的`requirements.txt`？ 答案： 使用 `pipenv` 的锁命令可以达到这个效果。

```

pipenv lock --requirements > requirements.txt
```

问题2：如何根据我的`main.py`来决定需要加载哪些库，并生成对应的`requirements.txt`？ 答案： 可以使用 `pipreqs` 这个工具来根据你的Python脚本或项目目录生成 `requirements.txt`。首先，你需要安装它：

```

pip install pipreqs
```

然后，你可以运行：

```

pipreqs /path/to/your/project
```

它会在指定目录中生成一个`requirements.txt`文件，该文件包含你的项目所需的所有库及其版本。



`pipreqs`的工作原理是扫描指定目录下的所有Python源文件（以`.py`结尾），然后识别这些文件中用到的外部库。基于这些信息，它生成一个`requirements.txt`文件。以下是具体步骤：

1. `pipreqs`遍历指定目录（包含子目录）中的所有`.py`文件。
2. 对于每一个Python文件，它会解析文件内容以查找`import`语句。
3. 它会识别这些`import`语句中用到的库，并与标准库中的模块进行对比，以确定它们是否为外部依赖。
4. 最后，它会生成一个包含所有识别到的外部库的`requirements.txt`文件。
