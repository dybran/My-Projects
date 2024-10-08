AWS Cloud Solution For 2 Company Websites Using A Reverse Proxy Technology 
============================================

**WARNING**: This infrastructure set up is **NOT** covered by AWS free tier. Therefore, ensure to **DELETE  ALL** the resources created immediately after finishing the project. Monthly cost may be shockingly high if resources are not deleted. Also, it is strongly recommended to set up a budget and configure notifications when your spendings reach a predefined limit. Watch [this video](https://www.youtube.com/watch?v=fvz0cphjHjg) to learn how to configure AWS budget.

#### Instructions On How To Submit Your Work For Review And Feedback

To submit your work for review and feedback - follow [**this instruction**](https://starter-pbl.darey.io/en/latest/submission.html).

#### Let us Get Started

You have been doing great work so far - implementing different Web Solutions and getting hands on experience with many great DevOps tools. In previous projects you used basic [Infrastructure as a Service (IaaS)](https://en.wikipedia.org/wiki/Infrastructure_as_a_service) offerings from AWS such as [EC2 (Elastic Compute Cloud)](https://en.wikipedia.org/wiki/Amazon_Elastic_Compute_Cloud) as rented Virtual Machines and [EBS (Elastic Block Store)](https://en.wikipedia.org/wiki/Amazon_Elastic_Block_Store), you have also learned how to configure [Key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) and basic [Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html).

But the power of Clouds is not only in being able to rent Virtual Machines - it is much more than that. From now on, you will start gradually study different Cloud concepts and tools on example of AWS, but do not be worried, your knowledge will not be limited to only AWS specific concepts - overall principles are common across most of the major Cloud Providers (e.g., [Microsoft Azure](https://azure.microsoft.com/en-us/) and [Google Cloud Platform](https://cloud.google.com)).

***NOTE:*** The next few projects will be implemented manually. Before you begin to automate infrastructure in the cloud, it is very important that you can build the solution manually. Otherwise, programming your automation may become frustrating very quickly.

You will build a secure infrastructure inside AWS [VPC (Virtual Private Cloud)](https://en.wikipedia.org/wiki/Amazon_Virtual_Private_Cloud) network for a fictitious company (*Choose an interesting name for it*) that uses [**WordPress CMS**](https://wordpress.com) for its main business website, and a **Tooling Website** (`https://github.com/<your-name>/tooling`) for their DevOps team. As part of the company's desire for improved security and performance, a decision has been made to use a [reverse proxy technology from **NGINX**](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/) to achieve this. 

Cost, Security, and Scalability are the major requirements for this project. Hence, implementing the architecture designed below, ensure that infrastructure for both websites, WordPress and Tooling, is resilient to Web Server's failures, can accomodate to increased traffic and, at the same time, has reasonable cost.

![](./images/tooling_project_15.png)

#### Starting Off Your AWS Cloud Project

There are few requirements that must be met before you begin:

1. Properly configure your AWS account and Organization Unit [Watch How To Do This Here](https://youtu.be/9PQYCc_20-Q)

    * Create an [AWS Master account](https://aws.amazon.com/free/). (*Also known as Root Account*)
    * Within the Root account, create a sub-account and name it **DevOps**. (You will need another email address to complete this) 
    * Within the Root account, create an **AWS Organization Unit (OU)**. Name it **Dev**. (We will launch Dev resources in there)
    * Move the **DevOps** account into the ***Dev*** **OU**.
    * Login to the newly created AWS account using the new email address.

2. Create a free domain name for your fictitious company at Freenom domain registrar [here](https://www.freenom.com).

3. Create a hosted zone in AWS, and map it to your free domain from Freenom. [Watch how to do that here](https://youtu.be/IjcHp94Hq8A) 

***NOTE*** : As you proceed with configuration, ensure that all resources are appropriately tagged, for example:

* Project: `<Give your project a name>`
* Environment: `<dev>`
* Automated: `<No>` (If you create a recource using an automation tool, it would be `<Yes>`)


#### Set Up a Virtual Private Network (VPC)

Always make reference to the architectural diagram and ensure that your configuration is aligned with it.

1. Create a [VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
2. Create subnets as shown in the architecture
3. Create a route table and associate it with public subnets
4. Create a route table and associate it with private subnets
5. Create an [Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)
6. Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accisble from the Internet)
6. Create 3 [Elastic IPs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html) 
7. Create a [Nat Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) and assign one of the Elastic IPs (*The other 2 will be used by [Bastion hosts](https://aws.amazon.com/quickstart/architecture/linux-bastion/))
8. Create a [Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#CreatingSecurityGroups) for: 

    * `Nginx Servers`: Access to Nginx should only be allowed from a [Application Load balancer (ALB)](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/). At this point, we have not created a load balancer, therefore we will update the rules later. For now, just create it and put some dummy records as a place holder. 
    * `Bastion Servers`: Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your workstation public IP address. To get this information, simply go to your terminal and type `curl www.canhazip.com`
    * `Application Load Balancer`: ALB will be available from the Internet
    * `Webservers`: Access to Webservers should only be allowed from the Nginx servers. Since we do not have the servers created yet, just put some dummy records as a place holder, we will update it later.
    * `Data Layer`: Access to the Data layer, which is comprised of [Amazon Relational Database Service (RDS)](https://aws.amazon.com/rds/) and [Amazon Elastic File System (EFS)](https://aws.amazon.com/efs/) must be carefully desinged - only webservers should be able to connect to **RDS**, while Nginx and Webservers will have access to **EFS Mountpoint**.

#### Proceed With Compute Resources 

You will need to set up and configure compute resources inside your VPC. The recources related to compute are: 

* [EC2 Instances](https://www.amazonaws.cn/en/ec2/instance-types/)
* [Launch Templates](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html)
* [Target Groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)
* [Autoscaling Groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html)
* [TLS Certificates](https://en.wikipedia.org/wiki/Transport_Layer_Security)
* [Application Load Balancers (ALB)](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) 

#### Set Up Compute Resources for Nginx

##### Provision EC2 Instances for Nginx

1. Create an EC2 Instance based on CentOS [Amazon Machine Image (AMI)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) in any 2 [Availability Zones (AZ) in any AWS Region](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) ([it is recommended to use the Region that is closest to your customers](https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/RegionsAndAZs.html)). Use EC2 instance of [T2 family](https://aws.amazon.com/ec2/instance-types/t2/) (e.g. t2.micro or similar)
2. Ensure that it has the following software installed:
        
    * `python`
    * `ntp`
    * `net-tools`
    * `vim`
    * `wget`
    * `telnet`
    * `epel-release`
    * `htop`

3. [Create an `AMI`](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/tkv-create-ami-from-instance.html) out of the EC2 instance

###### Prepare Launch Template For Nginx (One Per Subnet)

1. Make use of the AMI to set up a launch template
2. Ensure the Instances are launched into a public subnet 
3. Assign appropriate security group
4. Configure [Userdata](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html) to update `yum` package repository and install `nginx`

###### Configure Target Groups

1. Select Instances as the target type
2. Ensure the protocol `HTTPS` on secure TLS port `443`
3. Ensure that the health check path is `/healthstatus`
4. Register `Nginx` Instances as targets
5. Ensure that health check passes for the target group

###### Configure Autoscaling For Nginx 

1. Select the right launch template
2. Select the VPC
3. Select both public subnets
4. Enable Application Load Balancer for the AutoScalingGroup (ASG) 
5. Select the target group you created before
6. Ensure that you have health checks for both `EC2` and `ALB`
7. The desired capacity is 2
8. Minimum capacity is 2 
9. Maximum capacity is 4
10. Set [scale out](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroupLifecycle.html) if CPU utilization reaches 90%
11. Ensure there is an [`SNS`](https://docs.aws.amazon.com/sns/latest/dg/welcome.html) topic to send scaling notifications

#### Set Up Compute Resources for Bastion

###### Provision the EC2 Instances for Bastion

1. Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) per each Availability Zone in the same Region and same AZ where you created Nginx server
2. Ensure that it has the following software installed

    * `python`
    * `ntp`
    * `net-tools`
    * `vim`
    * `wget`
    * `telnet`
    * `epel-release`
    * `htop`
3. [Associate an Elastic IP](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#using-instance-addressing-eips-associating) with each of the Bastion EC2 Instances
4. Create an `AMI` out of the EC2 instance

###### Prepare Launch Template For Bastion (One per subnet)

1. Make use of the AMI to set up a launch template
2. Ensure the Instances are launched into a public subnet 
3. Assign appropriate security group
4. Configure Userdata to update `yum` package repository and install `Ansible` and `git`

###### Configure Target Groups

1. Select Instances as the target type
2. Ensure the protocol is `TCP` on port `22`
3. Register `Bastion` Instances as targets
4. Ensure that health check passes for the target group

###### Configure Autoscaling For Bastion 

1. Select the right launch template
2. Select the VPC
3. Select both public subnets
4. Enable Application Load Balancer for the AutoScalingGroup (ASG) 
5. Select the target group you created before
6. Ensure that you have health checks for both `EC2` and `ALB`
7. The desired capacity is 2
8. Minimum capacity is 2 
9. Maximum capacity is 4
10. Set scale out if CPU utilization reaches 90%
11. Ensure there is an `SNS` topic to send scaling notifications

#### Set Up Compute Resources for Webservers 

##### Provision the EC2 Instances for Webservers 

Now, you will need to create 2 separate launch templates for both the WordPress and Tooling websites

1. Create an EC2 Instance (`Centos`) each for WordPress and Tooling websites per Availability Zone (in the same Region).
2. Ensure that it has the following software installed

    * `python`
    * `ntp`
    * `net-tools`
    * `vim`
    * `wget`
    * `telnet`
    * `epel-release`
    * `htop`
    * `php`

3. Create an `AMI` out of the EC2 instance

##### Prepare Launch Template For Webservers (One per subnet)

1. Make use of the AMI to set up a launch template
2. Ensure the Instances are launched into a public subnet 
3. Assign appropriate security group
4. Configure Userdata to update `yum` package repository and install `wordpress` (*Only required on the WordPress launch template*)

#### TLS Certificates From [Amazon Certificate Manager (ACM)](https://aws.amazon.com/certificate-manager/)

You will need TLS certificates to handle secured connectivity to your Application Load Balancers (ALB).

1. Navigate to AWS ACM
2. Request a public wildcard certificate for the domain name you registered in Freenom
3. Use DNS to validate the domain name
4. Tag the resource


#### Configure Application Load Balancer (ALB)

##### Application Load Balancer To Route Traffic To NGINX 

Nginx EC2 Instances will have configurations that accepts incoming traffic only from Load Balancers. No request should go directly to Nginx servers. With this kind of setup, we will benefit from intelligent routing of requests from the **ALB** to Nginx servers across the 2 Availability Zones. We will also be able to [offload](https://avinetworks.com/glossary/ssl-offload/) SSL/TLS certificates on the **ALB** instead of Nginx. Therefore, Nginx will be able to perform faster since it will not require extra compute resources to valifate certificates for every request.

1. Create an Internet facing ALB
2. Ensure that it listens on `HTTPS` protocol (TCP port 443)
3. Ensure the ALB is created within the appropriate `VPC | AZ | Subnets`
4. Choose the Certificate from ACM
5. Select Security Group
6. Select Nginx Instances as the target group

##### Application Load Balancer To Route Traffic To Web Servers 

Since the webservers are configured for auto-scaling, there is going to be a problem if servers get dynamically scalled out or in. Nginx will not know about the new IP addresses, or the ones that get removed. Hence, Nginx will not know where to direct the traffic. 

To solve this problem, we must use a load balancer. But this time, it will be an internal load balancer. Not Internet facing since the webservers are within a private subnet, and we do not want direct access to them.

1. Create an Internal ALB
2. Ensure that it listens on `HTTPS` protocol (TCP port 443)
3. Ensure the ALB is created within the appropriate `VPC | AZ | Subnets`
4. Choose the Certificate from ACM
5. Select Security Group
6. Select webserver Instances as the target group
7. Ensure that health check passes for the target group

***NOTE:*** This process must be repeated for both WordPress and Tooling websites.

#### Setup EFS

[Amazon Elastic File System (Amazon EFS)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEFS.html) provides a simple, scalable, fully managed elastic [Network File System (NFS)](https://en.wikipedia.org/wiki/Network_File_System) for use with AWS Cloud services and on-premises resources. In this project, we will utulize EFS service and mount filesystems on both Nginx and Webservers to store data.

1. Create an EFS filesystem
2. Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer
3. Associate the Security groups created earlier for data layer.
4. Create an EFS access point. (Give it a name and leave all other settings as default)

#### Setup RDS 

***Pre-requisite***: Create a KMS key from [Key Management Service (KMS)](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html) to be used to encrypt the database instance.

[Amazon Relational Database Service (Amazon RDS)](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html) is a managed distributed relational database service by Amazon Web Services. This web service running in the cloud designed to simplify setup, operations, maintenans & scaling of relational databases. *Without RDS, Database Administrators (DBA) have more work to do, due to RDS, some DBAs have become jobless*

To ensure that yout databases are highly available and also have failover support in case one availability zone fails, we will configure a [multi-AZ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html) set up of [RDS MySQL database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html) instance. In our case, since we are only using 2 AZs, we can only failover to one, but the same concept applies to 3 Availability Zones. We will not consider possible failure of the whole Region, but for this AWS also has a solution - this is a more advanced concept that will be discussed in following projects.

To configure RDS, follow steps below:

1. Create a subnet group and add 2 private subnets (data Layer)
2. Create an RDS Instance for `mysql 8.*.*`
3. To satisfy our architectural diagram, you will need to select either **Dev/Test** or **Production** Sample Template. But to minimize AWS cost, you can select the **Do not create a standby instance** option under **Availability & durability** sample template (*The production template will enable Multi-AZ deployment*)
4. Configure other settings accordingly (*For test purposes, most of the default settings are good to go*). In the real world, you will need to size the database appropriately. You will need to get some information about the usage. If it is a highly transactional database that grows at 10GB weekly, you must bear that in mind while configuring the initial storage allocation, storage autoscaling, and maximum storage threshold.
5. Configure VPC and security (ensure the database is not available from the Internet)
6. Configure backups and retention
7. Encrypt the database using the KMS key created earlier
8. Enable [CloudWatch](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/monitoring-cloudwatch.html) monitoring and export `Error` and `Slow Query` logs (for production, also include `Audit`)

**Note** This service is an expensinve one. Ensure to review the monthly cost before creating. (***DO NOT LEAVE ANY SERVICE RUNNING FOR LONG***)

#### Configuring DNS with Route53

Earlier in this project you registered a free domain with Freenom and configured a hosted zone in [**Route53**](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html). But that is not all that needs to be done as far as DNS configuration is concerned. 

You need to ensure that the main domain for the WordPress website can be reached, and the subdomain for Tooling website can also be reached using a browser.

Create other records such as [`CNAME`, `alias` and `A` records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-concepts.html).

***NOTE***: You can use either `CNAME` or `alias` records to achieve the same thing. But `alias` record has better functionality because it is a faster to resolve DNS record, and can coexist with other records on that name. Read [here](https://support.dnsimple.com/articles/differences-between-a-cname-alias-url/#:~:text=The%20A%20record%20maps%20a,a%20name%20to%20another%20name.&text=The%20ALIAS%20record%20maps%20a,the%20HTTP%20301%20status%20code) to get to know more about the differences.

* Create an `alias` record for the root domain and direct its traffic to the `ALB` DNS name.
* Create an `alias` record for `tooling.<yourdomain>.com` and direct its traffic to the `ALB` DNS name.

#### Congratulations! 

![](./images/awesome_15.png)

You have just created a secured, scalable and cost-effective infrastructure to host 2 enterprise websites using various Cloud services from AWS. At this point, your infrastructure is ready to host real websites' load. Since it is a pretty expensive infrastructure to keep it up and running, we are going to start using [Infrastructure as Code](https://en.wikipedia.org/wiki/Infrastructure_as_code) tool [Terraform](https://www.terraform.io) to easily provision and destroy this set up. 

Move on for more amazing and challenging projects ahead!

