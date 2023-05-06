# Assignment-3 Infrastructure as a  code

## Purpose

We are going to start setting up our AWS infrastructure. This assignment will focus on setting up our networking resources such as Virtual Private Cloud (VPC), Internet Gateway, Route Table, and Routes. We will use Terraform for infrastructure setup and tear down.

## Requirments

Here is what you need to do for networking infrastructure setup:

Create Virtual Private Cloud (VPC)

Create subnets in your VPC. You must create 3 public subnets and 3 private subnets, each in a different availability zone in the same region in the same VPC.

Create an Internet Gateway. resource and attach the Internet Gateway to the VPC.

Create a public route table. Attach all public subnets created to the route table.

Create a private route table. Attach all private subnets created to the route table.

Create a public route in the public route table created above with the destination CIDR block 0.0.0.0/0 and the internet gateway created above as the target.


 ## Configuration of AWS
*We need to configure the aws profile in the terminal* 

we will use `aws configure --profile (demo/dev)` 

aws configure --profile  demo/dev

AWS Access Key ID [None]: enter access key here

AWS Secret Access Key [None]: enter secret access key hereKEY

Default region name [None]: default region name

Default output format [None]: what is output


## Usage Instructions 

Copy and paste into your Terraform variables(tfvars file), insert or update the
variables inside the file, and run `terraform init`:

Run `terraform plan` - command creates an execution plan, which lets you preview the changes that Terraform plans to make to your infrastructure

Run `terraform apply` - Terraform automatically creates a new execution plan as if you had run terraform plan prompts you to approve that plan, and takes the indicated actions.

# Assignment-4 Packer & AMIs

## Purpose 
Building Custom Application AMI using Packer, Continuous Integration: Add New GitHub Actions Workflow for Web App, Infrastructure as Code w/ Terraform, EC2 Instance

## Requirements

The EC2 instance must be launched in the VPC created by your Terraform template. You cannot launch the EC2 instance in the default VPC.

Create an EC2 security group for your EC2 instances that will host web applications.

Add ingress rule to allow TCP traffic on ports 22, 80, 443, and port on which your application runs from anywhere in the world.

This security group will be referred to as the application security group.

Create an EC2 instance with the following specifications. For any parameter not provided in the table below, you may go with default values. The EC2 instance should belong to the VPC you have created.

Application security group should be attached to this EC2 instance.
Make sure the EBS volumes are terminated when EC2 instances are terminated.

Parameter	                Value

Amazon Machine Image (AMI)	Your custom AMI

Instance Type	           `t2.micro`
Protect against             `accidental termination	No`
Root Volume Size	        `50`
Root Volume Type	        `General Purpose SSD (GP2)`

## Bootstrapping Database

The application is expected to automatically bootstrap the database at startup.

Bootstrapping creates the schema, tables, indexes, sequences, etc., or updates them if their definition has changed.

The database cannot be set up manually by running SQL scripts.

It is highly recommended that you use ORM frameworks such as Hibernate (for java), SQLAlchemy (for python), and Sequelize (for Node.js).

## Configuration of AWS

*We need to configure the aws profile in the terminal*

we will use `aws configure --profile (demo/dev)`

aws configure --profile  demo/dev

AWS Access Key ID [None]: enter access key here

AWS Secret Access Key [None]: enter secret access key hereKEY

Default region name [None]: default region name

Default output format [None]: what is output


## Usage Instructions
Copy and paste into your Terraform variables(tfvars file), insert or update the
variables inside the file, and run `terraform init`:

Run `terraform plan` - command creates an execution plan, which lets you preview the changes that Terraform plans to make to your infrastructure

Run `terraform apply` - Terraform automatically creates a new execution plan as if you had run terraform plan prompts you to approve that plan, and takes the indicated actions.


# Assignment-5 Addition of DB

## Purpose
In this assignment, you will update the Terraform template for the application stack to add the following resources:DB Security Group,S3 Bucket,RDS Parameter Group,RDS Instance,User Data,IAM Policy,IAM Role,Web Application

## Requirements

## DB Security Group

Create an EC2 security group for your RDS instances.

Add ingress rule to allow TCP traffic on the port 3306 for MySQL/MariaDB or 5432 for PostgreSQL.

The Source of the traffic should be the application security group. 

Restrict access to the instance from the internet.

This security group will be referred to as the database security group.

## S3 Bucket

Create a private S3 bucket with a randomly generated bucket name depending on the environment.

Make sure Terraform can delete the bucket even if it is not empty.

To delete all objects from the bucket manually use the CLI before you delete the bucket you can use the following AWS CLI command that may work for removing all objects from the bucket. aws s3 rm s3://bucket-name --recursive.

Enable default encryption for S3 BucketsLinks to an external site..

Create a lifecycle policy for the bucket to transition objects from STANDARD storage class to STANDARD_IA storage class after 30 days.

## RDS Parameter Group

A DB parameter group acts as a container for engine configuration values that are applied to one or more DB instances. Create a new parameter group to match your database (Postgres or MySQL) and its version. Then RDS DB instance must use the new parameter group and not the default parameter group.

## RDS Instance
WARNING: Setting Public accessibility to true will expose your instance to the internet.

Your RDS instance should be created with the following configuration. You may use default values/settings for any property not mentioned below.

Property	         Value
Database Engine	     MySQL/PostgreSQL
DB Instance Class	 db.t3.micro
Multi-AZ deployment	 No
DB instance identifier	csye6225
Master username	      csye6225
Master password	      pick a strong password
Subnet group	      Private subnet for RDS instances
Public accessibility	  No
Database name	       csye6225
Database security group should be attached to this RDS instance.

## User Data
EC2 instance should be launched with user dataLinks to an external site..
Database username, password, hostname, and S3 bucket name should be passed to the web application using user dataLinks to an external site..
The S3 bucket name must be passed to the application via EC2 user data.

## IAM Policy

WebAppS3 the policy will allow EC2 instances to perform S3 buckets. This is required for applications on your EC2 instance to talk to the S3 bucket.

Note: Replace * with appropriate permissions for the S3 bucket to create security policies.

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "s3:*"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::YOUR_BUCKET_NAME",
                "arn:aws:s3:::YOUR_BUCKET_NAME/*"
            ]
        }
    ]
}

## IAM Role
Create an IAM role EC2-CSYE6225 for the EC2 service and attach the WebAppS3 policy to it. You will attach this role to your EC2 instance.

## Web Application
The web application’s database must be the RDS instance launched by the Terraform template when running on the EC2 instance. You can no longer install/use the local database on the EC2 instance.


# Assignment-6 Domain Name System (DNS),Amazon Route 53 For DNS Service

## Domain Name System (DNS) Setup

###  Register Domain Name

Namecheap Domains: Only .me TLD domains are free for students.

1. Register a domain name with a domain registrar such as Namecheap (https://www.namecheap.com/domains/registration.aspx) . Namecheap offers a free domain with the Github Student Developer pack (https://education.github.com/pack) .

2. Your domain name can be anything.

## Configuring Amazon Route 53 For DNS Service

Your new domain will be set up to use the registrar’s name servers by default. You will need to make the following changes.

CREATE HOSTED ZONE FOR THE DOMAIN IN THE ROOT AWS ACCOUNT

This setup is done manually from the AWS console.
1. Create a public hosted zone (http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/CreatingHostedZone.html) in Amazon Route 53 (https://aws.amazon.com/route53/) for the domain yourdomainname.tld .

2. Configure Namecheap to use custom name servers (https://www.namecheap.com/support/knowledgebase/article.aspx/767/10/how-can-i-change-the- nameservers-for-my-domain) provided by Amazon Route 53 (http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/GetInfoAboutHostedZone.html) to use the Route53 name servers.

CREATE A SUBDOMAIN & HOSTED ZONE FOR THE DEV AWS ACCOUNT

This setup is done manually from the AWS console.

1. Create a public hosted zone (http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/CreatingHostedZone.html) in Amazon
Route 53 (https://aws.amazon.com/route53/) for the subdomain dev.yourdomainname.tld .

2. Configure name servers for the subdomain in the root account. See docs (https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/CreatingNewSubdomain.html) .


CREATE A SUBDOMAIN & HOSTED ZONE FOR THE DEMO AWS ACCOUNT

This setup is done manually from the AWS console.

1. Create a public hosted zone (http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/CreatingHostedZone.html) in Amazon Route 53 (https://aws.amazon.com/route53/) for the subdomain prod.yourdomainname.tld .

2. Configure name servers for the subdomain in the root account. See docs (https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/CreatingNewSubdomain.html) .


## Infrastructure as Code w/Terraform 
### DNS & EC2 Instance A Record

Route53 should be updated from the Terraform template.

The Terraform template should add/update A record to the Route53 zone so that your domain points to your EC2 instance and your web application is accessible thru http://your-domain-name.tld/ (http://your-domain-name.tld/) .

Your application must be accessible using root context i.e. http://your-domain-name.tld/ (http://https//your-domain-name.tld/) and not http://your-domain-name.tld/app-0.1/ (http://your-domain-name.tld/app-0.1/)


# Assignment #07 Application Logging & Metrics

Add IAM roles & policies needed to meet the assignment objectives to the Terraform Template.

## Application Logging & Metrics AMI Updates

1. Update your packer template to install (https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on- first-instance.html#download-CloudWatch-Agent-on-EC2-Instance-first) the Unified CloudWatch Agent (https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/UseCloudWatchUnifiedAgent.html) in your AMIs.

2. Your CloudWatch agent must be set up to start automatically when an EC2 instance is launched using your AMI.

## IAM Updates
Update the Terraform template to Update the IAM role attached to the EC2 instance for use with CloudWatch Agent (https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-iam-roles-for-cloudwatch-agent.html) .

## WebApp User Stories
1. As a user, I want all application log data to be available in CloudWatch.

2. As a user, I want metrics on API usage available in CloudWatch.

3. Create the following custom metrics for every API we have implemented in the web application.
The metrics data should be collected in CloudWatch. 1. Count the number of times each API is called.

4. You can retrieve custom metrics using either StatsD (https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom- metrics-statsd.html) or collectd (https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom-metrics-collectd.html) .

## Command to Configure CloudWatch Agent (Amazon Linux)
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
    -a fetch-config \
    -m ec2 \
    -c file:/opt/cloudwatch-config.json \
    -s
## Documentation CloudWatch Logs

Create IAM Roles and Users for Use With CloudWatch Agent (https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-iam-roles-for-cloudwatch-agent.html)

Install CloudWatch Agent Package on an Amazon EC2 Instance (https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html#download-CloudWatch-Agent-on-EC2-Instance-first)

## CloudWatch Metrics

Retrieve Custom Metrics with StatsD (https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom-metrics-statsd.html)
Retrieve Custom Metrics with collected (https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom-metrics-collectd.html)


# Assignment #08 Load Balancer Security,Autoscaling for EC2 Instances,Load Balancer For Your Web Application,JMeter Load Testing Script

## IAM Users, Roles & Policies
Add all IAM roles & policies needed to meet the assignment objectives to your Terraform template.

## Create Load Balancer Security Group

Create a security group for the load balancer to access the web application.

Add ingress rule to allow TCP traffic on ports 80 , and 443 from anywhere in the world.

This security group will be referred to as the load balancer security group.

## App Security Group Updates

Update the EC2 security group for your EC2 instances that will host web applications.

The ingress rule should allow TCP traffic on ports 22 and port on which your application runs . The Source of the traffic should be the load balancer security group. Restrict access to the instance from the internet.

## The Auto Scaling Application Stack
So far our web application has been accessible by the IP address of the EC2 instance on HTTP protocol. We will now disable direct access to our web application using the IP address of the EC2 instance. The web application will now only be accessible from the load balancer.

## Setup Autoscaling for EC2 Instances
Instead of launching standalone EC2 instances, we are now going to launch them in an auto-scaling group with a minimum of 1 instance and a maximum of 3. Use the default (or customize them as per your preference) for properties not listed below.

Launch Configuration

| Key                                                            | Value|
ImageId                                                    Your custom AMI
Instance Type                                              t2.micro            
KeyName                                                    YOUR_AWS_KEYNAME
AssociatePublicIpAddress                                   True
UserData                                                   SAME_USER_DATA_AS_CURRENT_EC2_INSTANCE
IAM Role                                                   SAME_AS_CURRENT_EC2_INSTANCE
Resource Name                                              asg_launch_config
Security Group                                             WebAppSecurityGroup

  
##  Auto Scaling Group
Note: You should add tags (https://docs.aws.amazon.com/AutoScaling/latest/DeveloperGuide/ASTagging.html) (AutoScalingGroup TagProperty (https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-as- tags.html) ) to the EC2 instances in your Auto Scaling Group.

Parameter                                                  Value
Cooldown                                                   60
LaunchConfigurationName                                    asg_launch_config
MinSize                                                    1
MaxSize                                                    3
DesiredCapacity                                            1
  
 

## AutoScaling Policies

Create the following autoscaling policies:

1. Scale up policy when is above 5% . Increment by 1 .

2. Scale down policy when is below 3% . Decrement by 1 .

## Setup Application Load Balancer For Your Web Application

EC2 instances launched in the auto-scaling group should now be load balanced.

Add a balancer resource to your Terraform template.

Set up an Application load balancer to accept HTTP traffic on port 80 and forward it to your application instances on whatever port it listens on.

You are not required to support HTTP to HTTPS redirection. Attach the load balancer security group to the load balancer.
average CPU usage
average CPU usage

## DNS Updates

Route53 should be updated from the Terraform template.

Route53 resource record for your domain name should now be an alias for your load balancer application.

The Terraform template should configure Route53 so that your domain points to your load balancer and your web application is accessible thru http://your-domain-name.tld/ (http://your-domain-name.tld/) .

Your application must be accessible using root context i.e. http://your-domain-name.tld/ (http://https//your-domain-name.tld/) and not http://your-domain-name.tld/app-0.1/(http://your- domain-name.tld/app-0.1/)

## Create JMeter Load Testing Script

Using Apache JMeter (http://jmeter.apache.org/) create tests that can be run against your application APIs.

JMeter tests need to make 500 concurrent API calls to your application.

JMeter tests should be stored in your application's GitHub repository.

