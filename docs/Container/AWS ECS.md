# AWS ECS(Acloudgrp)

[TOC]



## Deep dive with AWS ECS

### 1. Cluster

```shell
✗ aws ecs create-cluster --cluster-name deepdive
✗ aws ecs list-clusters
✗ aws ecs describe-clusters --clusters deepdive
✗ aws ecs delete-cluster --cluster deepdive
```

### 2. ECS agent

```shell
# Agent installed on each cluster instance, help them join cluster.
# github: https://github.com/aws/amazonecsagent
# Later this doc will also has example cli to show how to create cluster and scale it.

aws s3api create-bucket --bucket nickjj_deepdive
aws s3 cp ecs.config s3://nickjj_deepdive/ecs.config
aws s3 ls s3://nickjj_deepdive
```

### 3. Container Instances

```shell
✗ aws ec2 run-instances \
--image-id ami-057631c6a4834e06d \
--count 3 \
--instance-type t2.micro \
--iam-instance-profile Name=ecsInstanceRole \
--key-name TokyoKeyPair \
--security-group-ids sg-0cc23af0e1b4a5138 \
--user-data file://copy-ecs-config-to-s3 \
--tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=ECS_Dockerzon}]'

✗ aws ec2 describe-instance-status --instance-id i-02914f86
✗ aws ecs list-container-instances --cluster deepdive
✗ aws ecs describe-container-instances --cluster deepdive \
--container-instances ***(this is containerARN)

✗ aws ecs describe-clusters --cluster deepdive
✗ aws ec2 terminate-instances --instance-ids i-02914f86
```

### 4. Task definition

```shell
# 3 components of a task definition: Family, container definitions, volumes
✗ aws ecs register-task-definition --cli-input-json file://web-task-definition.json
✗ aws ecs list-task-definition-families
✗ aws ecs list-task-definitions
✗ aws ecs describe-task-definition --task-definition web:1

# Register a second task definition using the same task definition, the revision number increases by 1.

✗ aws ecs deregister-task-definition --task-definition web:2

# Generate a skeleton task definition to the terminal, an empty template.
✗ aws ecs register-task-definition --generate-cli-skeleton > template
```

### 5. Scheduler(services, running tasks, starting tasks)

```shell
# 3 ways to schedule
#			1. services, 2. running tasks, 3. starting tasks

# 3 steps for placing a service into your cluster
#   	1. compare the task definition's attributes to the state of the cluster
#			2. check how many service instances are running in an AZ
#			3. check how many service instances are running on container instances

# 3 service and task status: Pending, Runing, Stopped

# === 1st way: services (create service named web) ===

✗ aws ecs create-service \
--cluster deepdive \
--service-name web \
--task-definition web \
--desired-count 1

✗ aws ecs list-services --cluster deepdive
✗ aws ecs describe-services --cluster deepdive --services web
✗ aws ec2 describe-instances
✗ aws ecs update-service \
--cluster deepdive \
--service web \
--task-definition web \
--desired count 2
# 可能无法启动，因为两个instance用了同样的端口，如果有不同的instances主机，那么可以启动，放在同一个上面不行。除非我们改动task definition，里面不要用固短裤，使用随机的，配置成0

✗ aws ecs delete-service --cluster deepdive --service web
✗ aws ecs list-services --cluster deepdive

✗ aws ecs create-service --generate-cli-skeleton # 生成Json文件空白模版

# === 2nd way: run task ===
✗ aws ecs run-task --cluster deepdive --task-definition web --count 1
✗ aws ecs list-tasks --cluster deepdive #输出task的arn
✗ aws ecs stop-task --cluster deepdive --task arn:aws:....(就是上一个命令的输出arn值)

# === 3rd way: start task === 让你有机会指定在某个计算力强的instance上跑
✗ aws ecs list-container-instances --cluster deepdive #输出containerInstanceArns
✗ aws ecs start-task --cluster deepdive --task-definition web \
--container-instances arn:...(这里是上一个命令的输出，制定在哪个节点上运行)

```

### 6.ECR

```shell
# Authenticate your Docker client to Amazon ECR

✗ aws ecr get-login --no-include-email --region region

# Create a new repository
✗ aws ecr create-repository --repository-name deepdive/nginx

# Describe all repositories
✗ aws ecr describe-repositories

# List all images in the deepdive/nginx repository
✗ aws ecr list-images --repository-name deepdive/nginx

# Pull down the nginx image so we can push it later
✗ docker pull nginx:1.9

# Tag the nginx image
✗ docker tag nginx:1.9 xxx.dkr.ecr.us-east-1.amazonaws.com/deepdive/nginx:1.9

# Push the nginx image to your repository
✗ docker push xxx.dkr.ecr.us-east-1.amazonaws.com/deepdive/nginx

# Reregister the task definition that you edited
# Make sure you're in the deepdive/ folder when executing this command
✗ aws ecs register-task-definition --cli-input-json \
file://web-task-definition.json

# Run the new task definition
# Make sure you're in the deepdive/ folder when executing this command
✗ aws ecs run-task --cluster deepdive --task-definition web --count 1
```

### 7. Tear down the cluster

```shell
# Terminate the EC2 instance
✗ aws ec2 terminate-instances --instance-ids i-02914f86

# Delete the files in the S3 bucket
✗ aws s3 rm s3://nickjj_deepdive --recursive
		● Replace my bucket name with yours

# Delete the S3 bucket
✗ aws s3api delete-bucket --bucket nickjj_deepdive
		● Replace my bucket name with yours

# Delete the nginx repository and its images
✗ aws ecr delete-repository --repository-name deepdive/nginx --force

# Delete the cluster
✗ aws ecs delete-cluster --cluster deepdive
```



## Develop a Ruby on Rail application

### Step 1: Generating a new rails project

```shell
✗ cd ~rubyonrail
✗ docker run --rm --user "$(id -u):$(id -g)" -v "$PWD":/usr/src/app -w /usr/src/app rails:4 rails new --skip-bundle dockerzon
# now you can see a dockerzon directory under rubyonrail

✗ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world-xuhai   latest              707d01544108        3 months ago        244MB
xuhi/10m            latest              3ad86df9040c        3 months ago        11.7MB
ubuntu              16.04               13c9f1285025        4 months ago        119MB
busybox             latest              e4db68de4ff2        4 months ago        1.22MB
hello-world         latest              fce289e99eb9        9 months ago        1.84kB
rails               4                   2d0710c254a7        3 years ago         857MB
```

 

### Step 2: Copy file to EC2 instances, under the directory rails of home directory.

```shell
✗ scp -i "~/pem/TokyoKeyPair.pem" *.zip centos@ec2-3-112-200-44.ap-northeast-1.compute.amazonaws.com:rubyonrail

✗ cd ~/rubyonrail
✗ unzip 003.zip
✗ unzip 004.zip

✗ cd dockerzon
✗ docker-compose up #takes a long time, images include postgresql and 

✗ docker volume ls
# You can see dockerzon_redis and dockerzon_postgres here.
DRIVER              VOLUME NAME
local               3e1868db63e5d8fc3191a83bd9def87ed5af92cdb117fac2b9198449ffdd18a4
local               4a6bd07641c74e1f8675df79a23dd9816c7bb0cd3307f7b391f3dd9bd167ddf3
local               0693ed62915a9ccd4b3ef168d21e4e104af420e8df7698bbdfa8da0f2b93e202
local               dockerzon_postgres
local               dockerzon_redis
✗ docker network ls

# Now you can surf the website localhost. You will encounter errors. So do below: run command in the current running docker.
✗ docker exec --user "$(id -u):$(id -g)" dockerzon_dockerzon_1 rake db:reset
✗ docker exec --user "$(id -u):$(id -g)" dockerzon_dockerzon_1 rake db:migrate
# Now you can surf the website localhost.

# create dummy model and then delete it.
✗ docker exec --user "$(id -u):$(id -g)" dockerzon_dockerzon_1 rails g model Dummy foo
      invoke  active_record
      create    db/migrate/20191019093216_create_dummies.rb
      create    app/models/dummy.rb
      invoke    test_unit
      create      test/models/dummy_test.rb
      create      test/fixtures/dummies.yml
✗ docker exec --user "$(id -u):$(id -g)" dockerzon_dockerzon_1 rails d model Dummy foo
      invoke  active_record
      remove    db/migrate/20191019093216_create_dummies.rb
      remove    app/models/dummy.rb
      invoke    test_unit
      remove      test/models/dummy_test.rb
      remove      test/fixtures/dummies.yml

✗ docker exec -it dockerzon_dockerzon_1 bash
✗ docker exec -it dockerzon_dockerzon_1 rails c

✗ unzip 007.zip #choose replace all. make sure the working directory is rubeonrail

# Now migrate the database
docker-compose up
✗ docker exec --user "$(id -u):$(id -g)" dockerzon_dockerzon_1 rake db:migrate
# Now surf localhost:8000
✗ docker exec -it dockerzon_redis_1 redis-cli
127.0.0.1:6379> KEYS *
1) "stat:processed:2019-10-19"
2) "stat:processed"
3) "stat:failed:2019-10-19"
4) "queues"
5) "stat:failed"
6) "b1a861ecad37:1:1446eecc1cd9"
7) "dockerzon::cache:total_hits"
8) "processes"
127.0.0.1:6379> GET dockerzon::cache:total_hits
"4"
127.0.0.1:6379>

✗ docker exec -it dockerzon_dockerzon_1 rails c
Loading development environment (Rails 4.2.5)
irb(main):001:0> Javelin.all
  Javelin Load (0.3ms)  SELECT "javelins".* FROM "javelins"
  Javelin Load (0.3ms)  SELECT "javelins".* FROM "javelins"
=> #<ActiveRecord::Relation [#<Javelin id: 1, thrown: 8, created_at: "2019-10-19 09:44:17", updated_at: "2019-10-19 09:44:17">, #<Javelin id: 2, thrown: 4, created_at: "2019-10-19 09:44:51", updated_at: "2019-10-19 09:44:51">]>
irb(main):002:0>

```



## Preparing to deploy everything on AWS

### Build Nginx

```shell
# before build image, change server_names_hash_bucket_size 64 to 128 in nginx.conf
# in folder nginx
✗ docker build -t dockerzon_nginx .

# Below lines for testing.
# ✗ docker-compose up
# ✗ docker run --rm -p 80:80 --net dockerzon_default dockerzon_nginx
```



### Setup an S3 bucket

```shell
✗ aws s3api create-bucket --bucket ecs.xuhai.xyz --create-bucket-configuration LocationConstraint=ap-northeast-1

{
    "Location": "http://ecs.xuhai.xyz.s3.amazonaws.com/"
}

✗ aws s3 cp ecs.config s3://ecs.xuhai.xyz/ecs.config
upload: ./ecs.config to s3://ecs.xuhai.xyz/ecs.config

✗ aws s3 ls s3://ecs.xuhai.xyz/
2019-10-17 16:15:04         23 ecs.config
```



### Create security group before creating RDS

```shell
✗ aws ec2 create-security-group --group-name nick_SG_useast1 \
--description "Security group for nick ECS demo on ap-northeast-1"
✗ aws ec2 describe-security-groups --group-id sg-0cc23af0e1b4a5138

✗ aws ec2 authorize-security-group-ingress --group-id sg-5f63c627 \
--protocol tcp --port 22 --cidr 0.0.0.0/0
✗ aws ec2 authorize-security-group-ingress --group-id sg-0cc23af0e1b4a5138 \
--protocol tcp --port 80 --cidr 0.0.0.0/0
✗ aws ec2 authorize-security-group-ingress --group-id sg-0cc23af0e1b4a5138 \
--protocol tcp --port 5432 --cidr 0.0.0.0/0
✗ aws ec2 authorize-security-group-ingress --group-id sg-0cc23af0e1b4a5138 \
--protocol tcp --port 6379 --cidr 0.0.0.0/0


```



### Setup RDS for postgres(25% markup to use RDS)

```shell
✗ aws rds create-db-instance \
		--engine postgres \
		--no-multi-az \
		--no-publicly-accessible \
		--vpc-security-group-ids sg-0cc23af0e1b4a5138 \
		--db-instance-class db.t2.micro \
		--allocated-storage 20 \
		--db-instance-identifier dockerzon-production \
		--db-name dockerzon_production \
		--master-username dockerzon \
		--master-user-password intergalacticzebramoldfactory
# Endpoint: dockerzon-production.cigcwuufliwo.ap-northeast-1.rds.amazonaws.com
		
# To change password, you can use below command
✗ aws rds modify-db-instance --db-instance-identifier dockerzon-production --master-user-password ....

# To delete rds instance:
✗ aws rds delete-db-instance --db-instance-identifier dockerzon-production ...
```



### Setup ElasticCache for Redis

```shell
✗ aws elasticache create-cache-cluster \
 		--engine redis \
 		--security-group-ids sg-0cc23af0e1b4a5138 \
 		--cache-node-type cache.t2.micro \
 		--num-cache-nodes 1 \
 		--cache-cluster-id dockerzon-production
# Endpoint: dockerzon-production.yytj0n.0001.apne1.cache.amazonaws.com:6379

✗ aws elasticache describe-cache-clusters
✗ aws elasticache describe-cache-clusters --show-cache-node-info # You can find endpoint

# To delete redis
✗ aws elasticache delete-cache-cluster --cache-cluster-id dockerzon-production

```



### Setup ELB

```shell
✗ aws ec2 describe-subnets
# Now write down subnets where DefaultForAz is True
subnet-f0eea2ab subnet-a1aebce8 subnet-07a4402c

✗ aws elb create-load-balancer --load-balancer-name dockerzon-web --listeners "Protocol=HTTP,LoadBalancerPort=80,InstanceProtocol=HTTP,InstancePort=80" --subnets subnet-f0eea2ab subnet-a1aebce8 subnet-07a4402c --security-groups sg-0cc23af0e1b4a5138

Output:
{
    "DNSName": "dockerzon-web-1820209217.ap-northeast-1.elb.amazonaws.com"
}

✗ aws elb describe-load-balancers

# Now change the timeout to 5s instead of 60s, for Demo purpose
✗ aws elb modify-load-balancer-attributes --load-balancer-name dockerzon-web --load-balancer-attributes "{\"ConnectionSettings\":{\"IdleTimeout\":5}}"

✗ aws elb configure-health-check --load-balancer-name dockerzon-web --health-check "Target=HTTP:80/health_check,Timeout=5,Interval=30,UnhealthyThreshold=2,HealthyThreshold=10"

# To delete ELB
✗ aws elb delete-load-balancer --load-balancer-name dockerzon-web
```

 

### Now profiling the Ruby on Rails application

```shell
✗ docker exec --user "$(id -u):$(id -g)" dockerzon_dockerzon_1 rake db:reset

✗ docker pull williamyeh/wrk
Using default tag: latest
latest: Pulling from williamyeh/wrk
4fe2ade4980c: Pull complete
c4d7e348633d: Pull complete
3e403d3ebdda: Pull complete
bdb672ee55d9: Pull complete
2bfb714176a4: Pull complete
Digest: sha256:78adc0d9d51a99e6759e702a08d03eaece81c890ffcc9790ef9e5b199d54f091
Status: Downloaded newer image for williamyeh/wrk:latest

✗ hostname -I | cut -d ' ' -f 1
172.31.40.214

✗ docker run --rm williamyeh/wrk -t10 -c50 -d10s http://172.31.40.214:8000
Running 10s test @ http://172.31.40.214:8000
  10 threads and 50 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.80ms  456.93us  18.26ms   96.40%
    Req/Sec   356.93     19.90   373.00     95.00%
  3558 requests in 10.03s, 4.99MB read
Requests/sec:    354.69
Transfer/sec:    509.07KB

```



## Deploy Everything with Amazon ECS

### Step 1: create ECS cluster  

```shell
✗ aws ecs create-cluster --cluster-name production

```



### Step 2: create bucket

```shell
✗ aws s3api create-bucket --bucket ecs.xuhai.xyz --create-bucket-configuration LocationConstraint=ap-northeast-1

{
    "Location": "http://ecs.xuhai.xyz.s3.amazonaws.com/"
}

✗ aws s3 cp ecs.config s3://ecs.xuhai.xyz/ecs.config
upload: ./ecs.config to s3://ecs.xuhai.xyz/ecs.config

✗ aws s3 ls s3://ecs.xuhai.xyz/
2019-10-17 16:15:04         23 ecs.config
```



### Step 3: login ECR and push 2 images

```shell
✗ brew cask install docker  #Install docker if you haven't installed it.

✗ $(aws ecr get-login --no-include-email) #"--no-iclude-email" is a must to login ECR
# or use: $(aws ecr get-login --no-include-email --region ap-northeast-1)

✗ aws ecr create-repository --repository-name dockerzon/dockerzon
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:ap-northeast-1:581725073534:repository/dockerzon/dockerzon",
        "registryId": "581725073534",
        "repositoryName": "dockerzon/dockerzon",
        "repositoryUri": "581725073534.dkr.ecr.ap-northeast-1.amazonaws.com/dockerzon/dockerzon",
        "createdAt": 1571302293.0,
        "imageTagMutability": "MUTABLE"
    }
}

✗ aws ecr create-repository --repository-name dockerzon/nginx

✗ docker tag dockerzon_dockerzon:latest 581725073534.dkr.ecr.ap-northeast-1.amazonaws.com/dockerzon/dockerzon:latest
✗ docker push 581725073534.dkr.ecr.ap-northeast-1.amazonaws.com/dockerzon/dockerzon:latest
# Here, if you get an error of "no basic auth credentials", that's because you are login into another region instead of ap-northeast-1. It might login in west-2 Oregon region.

✗ docker tag dockerzon_nginx:latest 581725073534.dkr.ecr.ap-northeast-1.amazonaws.com/dockerzon/nginx:latest
✗ docker push 581725073534.dkr.ecr.ap-northeast-1.amazonaws.com/dockerzon/nginx
# The "latest" is not needed. 


```



### Step4: Create 3 instances and join the ECS cluster

```shell
aws ec2 describe-key-pairs # To get the key-name
# Before  moving the next step, create IAM role ecsInstanceRole with 2 policies attached: 
# S3ReadOnlyAccess, EC2ContainerServiceforEC2Role

# ami-057631c6a4834e06d =Amazon ECS-Optimized Amazon Linux 2 AMI-2-0-20191014-AutogenByAWSMP

# modify the copy-ecs-config-to-s3 file. change the bucket name to ecs.xuhai.xyz

✗ aws ec2 run-instances \
--image-id ami-057631c6a4834e06d \
--count 3 \
--instance-type t2.micro \
--iam-instance-profile Name=ecsInstanceRole \
--key-name TokyoKeyPair \
--security-group-ids sg-0cc23af0e1b4a5138 \
--user-data file://copy-ecs-config-to-s3 \
--tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=ECS_Dockerzon}]'

# When you delete your cluster Production in AWS console, these 3 EC2 instances will not be deleted automatically. I have to terminate them manually.

✗ vi copy-ecs-config-to-s3
#!/bin/bash

yum install -y aws-cli
aws s3 cp s3://ecs.xuhai.xyz/ecs.config /etc/ecs/ecs.config

✗ vi ecs.config
ECS_CLUSTER=production

✗ aws ecs list-container-instances --cluster production #To check if 3 instances joined the cluster


```



### Step 5: register services with ECS cluster, 3 containers

```shell
✗ docker-compose up -d
✗ docker exec dockerzon_dockerzon_1 rake secret #This command to generate secret_token
58cd52878916ed7328c7b771c42cebeffbbf4c59e49c8ec39c9f334aa781559e492bd572d13d6b5174270db7f2f6a3bf80f5c6e9b9a78cc7dce081fd66369cd9
✗ docker-compose stop

# Image: 581725073534.dkr.ecr.ap-northeast-1.amazonaws.com/dockerzon/nginx
# Image: 581725073534.dkr.ecr.ap-northeast-1.amazonaws.com/dockerzon/dockerzon
# PostgreSQL Endpoint: dockerzon-production.cigcwuufliwo.ap-northeast-1.rds.amazonaws.com
# Redis Endpoint: dockerzon-production.yytj0n.0001.apne1.cache.amazonaws.com

✗ aws ecs register-task-definition --cli-input-json file://worker-task-definition.json
✗ aws ecs register-task-definition --cli-input-json file://web-task-definition.json

✗ aws ecs register-task-definition --cli-input-json file://db-reset-task-definition.json
✗ aws ecs run-task --cluster production --task-definition db-reset --count 1
✗ aws ecs deregister-task-definition --task-definition db-reset:3 #3 is the revision number

✗ aws ecs register-task-definition --cli-input-json file://db-migrate-task-definition.json
✗ aws ecs run-task --cluster production --task-definition db-migrate --count 1

✗ aws ecs list-task-definitions
{
    "taskDefinitionArns": [
        "arn:aws:ecs:ap-northeast-1:581725073534:task-definition/db-migrate:4",
        "arn:aws:ecs:ap-northeast-1:581725073534:task-definition/web:3",
        "arn:aws:ecs:ap-northeast-1:581725073534:task-definition/worker:3"
    ]
}
```



### Step 6: create services

```shell
# In production directory
✗ aws ecs create-service --cli-input-json file://web-service.json
✗ aws ecs describe-services --cluster production --services web

✗ aws ecs create-service --cli-input-json file://worker-service.json
✗ aws ecs describe-services --cluster production --services worker
```





```shell
aws acm request-certificate --domain-name dockerzon.xuhai.xyz
aws acm describe-certificate 
aws acm list-certificate
```



