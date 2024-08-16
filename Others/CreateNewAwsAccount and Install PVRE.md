## Create new AWS Account and install PVRE

#### Step 1: Create new AWS account

Login https://isengard.amazon.com/console-access

Click "Create/Register Account", then click "Create" to create account

![image-20230907133928645](/Users/xuhi/Library/Application Support/typora-user-images/image-20230907133928645.png)

Select ap-east-1 (HK region) which is optional, and click "submit"

![image-20230907134010730](/Users/xuhi/Library/Application Support/typora-user-images/image-20230907134010730.png)



At somewhere(i forgot), you need to add Admin role. ( I guess it's not mandatory)

#### Step 2: Install PVRE

Link: https://w.amazon.com/bin/view/AWS/Safety_Infrastructure/Products/Varia/Oncall/Runbook/PVRE_Onboarding_Script

see the section "Instructions"

Download the zip file and unzip it, then run "sh PVRE-SSM-onboarding.sh"

enter regions such as: "ap-northeast-1,us-east-1,us-west-2,ap-east-1,ap-northeast-2,ap-southeast-1,ap-southeast-2,us-west-1"

After that, change the Cron/Rate expression to "cron(0 0 0 * * ?)" as follows: 

![image-20230907135917488](/Users/xuhi/Library/Application Support/typora-user-images/image-20230907135917488.png)

