## Create a new CDK project using Typescript



#### Step 1: Create new directory and initiate with `cdk init`

Use the following command to initiate a new project

```shell
mkdir sagemind
cd sagemind

cdk init app --language typescript
```

Upgrade your cdk version if needed:

``` shell
# check your current version
cdk --version
2.92.0 (build bf62e55)

# upgrade it using sudo
sudo npm install -g aws-cdk
changed 2 packages in 2m

cdk --version
2.96.2 (build 3edd240)

# run npm install
npm install
```



#### Step 2: Modify tsconfig.json

In our project, data will be imported from the /config/profile-config.json file. By default, TypeScript tries to import a JSON file but does not have the appropriate configuration to handle JSON modules, which leads to an error report at this time. The error looks like below:

```shell
Cannot find module '../config/project-config.json'. Consider using '--resolveJsonModule' to import module with '.json' extension.
```

To solve this problem, it is necessary to modify the tsconfig.json file by adding below 2 lines:

```json
{
  "compilerOptions": {
    "resolveJsonModule": true, // 允许JSON文件的导入和随后的模块绑定。
    "esModuleInterop": true, // 允许使用新的ECMAScript模块导入所有内容。
  },
}

```

The tsconfig.json will look like:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["es2020"],
    "declaration": true,
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": false,
    "noUnusedParameters": false,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": false,
    "inlineSourceMap": true,
    "inlineSources": true,
    "experimentalDecorators": true,
    "strictPropertyInitialization": false,
    "resolveJsonModule": true, // 允许JSON文件的导入和随后的模块绑定。
    "esModuleInterop": true, // 允许使用新的ECMAScript模块导入所有内容。
    "typeRoots": ["./node_modules/@types"]
  },
  "exclude": ["node_modules", "cdk.out"]
}

```



#### Step 3: Initiate Git

In the work directory, run `git init`:

```shell
git init

# actually, you don't need to run it because it's done when you run `cdk init app --language typescript`
```

Then, create a new repo named `sagemind` in GitHub without readme.md and gitignore files. After that

``` shell
git remote add origin git@github.com:awsxuhi/sagemind.git
git branch -M main
git push -u origin main
```



#### Step 4: Leverage dotenv to set ENV

Create a new file `.env` with the content as follows:

```shell
CDK_DEFAULT_ACCOUNT=581725073534
CDK_DEFAULT_REGION=us-east-1
```

Add below lines into bin/sagemind.ts:

```typescript
import "dotenv/config";

const ENV = {
  account: process.env.CDK_DEFAULT_ACCOUNT,
  region: process.env.CDK_DEFAULT_REGION,
};

new SageMindStack(app, "SageMindStack", {
  env: ENV,
  description: "(xuhi@) - This is a stack to deploy a Question Answering Bot on AWS. [2023.09.19]",
});
```

Install dotenv:

```shell
npm install dotenv
```

