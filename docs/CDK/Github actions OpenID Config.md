







URL: https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services



### Step 1: configure OpenID Connect

![image-20240721112100598](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721112100598.png)

### Step 2: configure IAM role to trust Github

Step 2.1: Download the stack yaml file from GitHub repo: https://github.com/aws-actions/configure-aws-credentials by clicking the link showed in the screenshot below.

![image-20240721113552392](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721113552392.png)

Step 2.2: Go to the CloudFormation console and create stack with the downloaded file

![image-20240721113906675](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721113906675.png)

![image-20240721114450176](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721114450176.png)

After the installation of the stack, the new role was created as follows:

![image-20240721131039581](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721131039581.png)

Here we can also delete "StringLike" part, so that it applies to all repos in Github as follows:

![image-20240722001153923](/Users/xuhi/Library/Application Support/typora-user-images/image-20240722001153923.png)





Now, let's add permission by attaching policies (e.g., AdminstratorAccess)

![image-20240721131142715](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721131142715.png)

After that, the console shows:

![image-20240721131252977](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721131252977.png)



### Step3: In GitHub repo, configure Environment and Variables

Create a environment named production (You can create 2 environment, one for production, the other for development). And in the production environment, you click "Add variable"

![image-20240721131945040](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721131945040.png)

Here the value is the arn of the role (refer to below screen shot)

![image-20240721132024755](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721132024755.png)

Add the Region variable:

![image-20240721132211862](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721132211862.png)



### Step 4: Create workflows Yaml file in github repo

![image-20240721132349887](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721132349887.png)

![image-20240721132422498](/Users/xuhi/Library/Application Support/typora-user-images/image-20240721132422498.png)

Now, after you commit the code and push it to GitHub. Github Actions will do the CICD and deploy the app on AWS.