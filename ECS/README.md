## DEPLOYMENT OF DJANGO APP STACK USING CF AND ECS

## What it does :

It creates a autoscalable stack for django blog application using cloudformation and ECS.Here are few specs
- An Autoscalable Stack of EC2 Instances (under load balancer) is brought up using cloudformation.
- This stack has ecs agent running on them which registers these instances under desired ECS cluster.
- I have created a docker image avichalbadaya/django-sample-app built over centos 6 , installed with basic packages , django app and nginx as proxy server .
- This image will run in ec2 instances which are registered under our cluster using ecs tasks /services we defined.
- All the stack resources except Cluster/Tasks and Service are created using Cloudformation and these ecs componenents are created by running "ecs_script" file.
- All aws resoures are put under defined vpc and subnet .

Scaling :
- Scaling of Web server (ec2 instances) happens automatically and could be modified in CF template .
- Scaled up instances comes under LB and LB only puts them once we have service running.

** Most of the settings for centos are similar to what we did in /cf_scripts/os_pkg_installer and /cf_scripts/django_app_installer in cloudformation stack of orinigal task.

Files:
- ECS/Dockerfile : Dockerfile for our Django app over centos6
- ECS/ecs.config : Config file which is put in ec2 instance to identify correct cluster (s3 link https://s3.amazonaws.com/sample-django-cf-scripts/ecs.config)
- ECS/ecsInstanceRole_policy : IAM role policy for ecsInstanceRole used to assign to ec2 instances.
- ECS/ecs_cf_template.json : Cloudformation template for ecs agent enabled stack .
- ECS/ecs_package_installer : Installer file called by CF template to do some basic settings like relinking ecs agent to correct cluster .
- ECS/ecs_script : three command to create ecs cluster , register task and create service. 
- ECS/task_def.json : sample ecs task definition .


How to run it :
- Step 1 : Install AWS CLI or you can use AWS console to create few things :
    1. Create a IAM role named "ecsInstanceRole" with policy similar to ECS/ecsInstanceRole_policy file.
    2. Create ECS cluster by running first command from ECS/ecs_script file or from AWS console and name it "BehanceTestCluster"
- Step 2 :To deploy ecs_cf_template.json , you will need to make very minimal changes to this template :
    1. VpcId ( this is the vpc under which you want to deploy this app)
    2. InstanceWebSubnets (provide subnet under this vpc, present in us-east-1a as we are using this region)
    3. ELBInternetSubnets (provide subnets under defined vpc for atleast 2 regions)
- Step 3 : Once you make these changes , you can go to aws console and upload this template in Cloudformation service , create stack. Just follow next clicks and create it.
- Step 4 : Once the stack is up you should see your instances registerd under BehanceTestCluster cluster . Next is to create task and run them as service . You need to run next two commands from ECS/ecs_script file to register task and create service or you can create them from console. If using console , you can refer ECS/task_def.json file. 
- You should see a running task in your cluster . Website should be accesible from DNS of loadbalancer , port 80. 

** Make sure that we have internet connectivity as we are downloading django app and installation files from internet.

