

Git logs

```shell
commit 32360ec1b0f840654704f86cfdc323ddd0b31d27 (HEAD -> main, origin/main)
Author: Hai Xu <xuhi@amazon.com>
Date:   Sun Feb 26 23:51:37 2023 +0800

    commit before push to github

commit ce533eb549e78d4cb6934380988f6fa3aeaee5f2
Author: Hai Xu <xuhi@amazon.com>
Date:   Tue Feb 21 21:44:16 2023 +0800

    commit before add amplify

commit fe4caef2cb0d0a9d3c2537c066c6c31afc46d241
Author: Hai Xu <xuhi@amazon.com>
Date:   Thu Jan 26 16:56:24 2023 +0800

    Initialize project using Create React App
(END)
```



From the directory of the palette, run

```shell
cd palette

> amplify init
Note: It is recommended to run this command from the root of your app directory
? Enter a name for the project palette
The following configuration will be applied:

Project information
| Name: palette
| Environment: dev
| Default editor: Visual Studio Code
| App type: javascript
| Javascript framework: react
| Source Directory Path: src
| Distribution Directory Path: build
| Build Command: npm run-script build
| Start Command: npm run-script start

? Initialize the project with the above configuration? Yes
Using default provider  awscloudformation
? Select the authentication method you want to use: AWS profile

For more information on AWS Profiles, see:
https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html

? Please choose the profile you want to use aws-amplify
Adding backend environment dev to AWS Amplify app: d39m54x36ld0ye

Deployment completed.
Deployed root stack palette [ ======================================== ] 4/4
	amplify-palette-dev-215313     AWS::CloudFormation::Stack     CREATE_COMPLETE                Tue Feb 21 2023 2
	UnauthRole                     AWS::IAM::Role                 CREATE_COMPLETE                Tue Feb 21 2023 2
	DeploymentBucket               AWS::S3::Bucket                CREATE_COMPLETE                Tue Feb 21 2023 2
	AuthRole                       AWS::IAM::Role                 CREATE_COMPLETE                Tue Feb 21 2023 2

✔ Help improve Amplify CLI by sharing non sensitive configurations on failures (y/N) · no
Deployment bucket fetched.
✔ Initialized provider successfully.
✅ Initialized your environment successfully.

Your project has been successfully initialized and connected to the cloud!

Some next steps:
"amplify status" will show you what you've added already and if it's locally configured or deployed
"amplify add <category>" will allow you to add features like user login or a backend API
"amplify push" will build all your local backend resources and provision it in the cloud
"amplify console" to open the Amplify Console and view your project status
"amplify publish" will build all your local backend and frontend resources (if you have hosting category added) and provision it in the cloud

Pro tip:
Try "amplify add api" to create a backend API and then "amplify push" to deploy everything

```

I am running:

```shell
> amplify add auth
Using service: Cognito, provided by: awscloudformation

 The current configured provider is Amazon Cognito.

 Do you want to use the default authentication and security configuration? Default configuration with Social Provider (Federation)
 Warning: you will not be able to edit these selections.
 How do you want users to be able to sign in? Username
 Do you want to configure advanced settings? No, I am done.
 What domain name prefix do you want to use? palette
 Enter your redirect signin URI: http://localhost:3000/
? Do you want to add another redirect signin URI Yes
 Enter your redirect signin URI: https://palette.auth.ap-northeast-1.amazoncognito.com/oauth2/idpresponse/
? Do you want to add another redirect signin URI No
 Enter your redirect signout URI: http://localhost:3000/signout/
? Do you want to add another redirect signout URI Yes
 Enter your redirect signout URI: https://palette.xuhi.solutions.aws.a2z.org.cn/signout/
? Do you want to add another redirect signout URI No
 Select the social providers you want to configure for your user pool:
✅ Successfully added auth resource palettee015be6a locally

✅ Some next steps:
"amplify push" will build all your local backend resources and provision it in the cloud
"amplify publish" will build all your local backend and frontend resources (if you have hosting category added) and provision it in the cloud

```

Next step:

 How do you want users to be able to sign in? Username
 Do you want to configure advanced settings? No, I am done.
 What domain name prefix do you want to use? palette
 Enter your redirect signin URI: http://localhost:3000/
? Do you want to add another redirect signin URI Yes
 Enter your redirect signin URI: https://p.xuhi.solutions.aws.a2z.org.cn/
? Do you want to add another redirect signin URI No
 Enter your redirect signout URI: http://localhost:3000/
? Do you want to add another redirect signout URI Yes
 Enter your redirect signout URI: https://p.xuhi.solutions.aws.a2z.org.cn/
? Do you want to add another redirect signout URI No



Create Userpool

```shell
we need to provide:
1. user pool name
2. app client name

```

![image-20230225205358918](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225205358918.png)



![image-20230225210319266](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225210319266.png)

![image-20230225230921219](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225230921219.png)

![image-20230225232026832](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225232026832.png)

Now, let's run "amplify push" to complete the import procedure.

![image-20230225232257659](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225232257659.png)

After running "amplify push", the was-exports.ts was automatically populated with your chosen Amazon Cognito resource information. 



![image-20230225232503839](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225232503839.png)



Important Note: You should select "Cognito User Pool" in the following UI. Otherwise, the import auth will not import the necessary configuration (such as scope, redirectSignIn, etc) in the section of "oauth".

==



![image-20230225235402183](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225235402183.png)

Click federated sign in:

![image-20230225235429924](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225235429924.png)



When I login with user pool email/password, it shows

![image-20230225235318522](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225235318522.png)

When I click AmazonEmployee, it shows:

![image-20230225235510665](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225235510665.png)

```javascript
getUser().then((userData) => {setUser(userData); console.log(userData)});
```

![image-20230225235749578](/Users/xuhi/Library/Application Support/typora-user-images/image-20230225235749578.png)

###### 



to host website:

![image-20230226232256506](/Users/xuhi/Library/Application Support/typora-user-images/image-20230226232256506.png)







add app client Palette

app client id: 568pllvkjo75l52opn70hht3d4

app client secret: 1qfvemofn9i35aberresv95pn575ln4l33ocbhpjtit655vfuh5d

