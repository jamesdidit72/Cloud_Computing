
## AMI

- Short for Amazon Machine Image
- Saves a machine's settings 


### Creating an ec2 instance from AMI

- Steps are similar to normal ec2 instance using a default AMI
- There are still some instance options to confirm, but first step is taken care of
- Still need some info
    - Security Group ID
    - VPC Subnet
    - Any other dependencies
    - Type of instance, size of EBS - storage required
    - Whether a public IP is required

### Dealing with Demand

- Big advantage of AWS and similar cloud services is the ability to scale with traffic
- AWS does provide the ability to automatically scale with demand
- Start with monitoring with Cloudwatch
    - This raises when a level is reached
    - Spin up autosclaing group
    - A load balancer then rebalances the demand.
- It is also possible to deploy architecture in multiple availability zones to defend against potential data center problems.

Have to create a listener group that checks machines are up and running.

### Basics of Setting up monitoring

- When viewing an instance, select monitoring.
- This shows stats such as CPU utlisation percentage, disk write, and so forth
- Can add information to a dashboard


- need to enable detailed montioring under `manage detailed monitoring`

    -this does cost more, so use with caution

- add the monitoring to a dashboard of choice

- next, in instances, either select the `+` sign next to your instances "alarm status" field, or enter the alarm creation menu via `actions -> monitor and troubleshoot ->Manage CloudWatch Alarms`

- Name your alarm, and set the threshold you would like to be notified by

- if you have an SNS group already set up, you can link it here

- Otherwise, enter the Cloudwatch dashboard and edit the alarm

- Add a new SNS group, and add the email addresses you would like to have notified when the alarm is triggered

- You should now receive an email asking you to confirm the subscription

- Congratulations, AWS will now let you know when your instance triggers an alarm!


Full guide to editing or setting up an alarm [here](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Edit-CloudWatch-Alarm.html)

More on SNS groups [here](https://docs.aws.amazon.com/sns/latest/dg/sns-create-topic.html)

## Monitoring Concepts

- A variety of things may need to be monitored
- This depends on the app and infrastructure, but a general guideline (applicable to our example app) might be:
    - CPU utilisation
    - Number of requests, response times, latency
    - The Firewall
- Which particular resources are to be monitored and and how frequently is also important. For example, some resources may only need to be checked over once every couple of hours
- Other questions include the choice of tools, and who will be responsible for keeping track of monitoring and responding
- Also need to ensure the correct people are notified when alarms are triggered

#### The 4 Golden Signals of Monitoring

These are considered the basics of monitoring virtual infrastructure. They are:

- Latency
    - the time taken to service a request.
    - Important because slow responses indicate performance degradation or insufficient resources
- Traffic
    - Number and type of requests with time
    - Important as this shows which services are beign used and how often (e.g. how many database requests an app is havign to make or how many sessions are active concurrently)
- Errors
    - The rate of requests returning an error code
- Saturation
    - How close to maximum utilisation resources are
    - Very important as ability to provide service may decrease the closer to full utilisation an instance is


## Responding to Monitoring

AWS' default monitoring service is Amazon Cloudwatch, which can be used for most of their resources. Key is to attempt to automate responses to monitoring. This can be done most easily with auto-scaling groups. For example, if demand peaks and services go beyond a particular utilisation, it would be useful to spin up additional instances. Defence against DDoS attacks and similar types of attempts by potentially malicious users is also available as a service, though the technical details are beyond the scope of these notes.

- Aamazon offers notifications to the user, which were implemented as previously described. 
- There are also services to queue requests (SQS, or Simple Queueing Service)
    - This holds requests and processes them in turn, avoiding reqturning error codes.
- Amazon Lambdas are a way of increasing capacity by only the amount used.


A good idea is to split various metrics into separate dashboards, so that particular signals or instance types might be monitored by dedicated teams (for example a team dedicated to monitoring and analysing traffic, or a team concerned with saturation of a database server)

## Automating Monitoring on a sample app

- Application Load Balancer
- Autoscaling Group
- Launch template configuration - how many instances should be running at all times
    - Minimum and maximum required (e.g. min=2, max=3)
- Policy on scaling out and scaling in
    - This is necessary to avoid paying for services that are not needed

- Scaling on Demand (Terminology)
    - Scaling Up versus scaling out: Scaling up means making components on an instance bigger to meet demand (e.g. increasing CPU capacity). Scaling outmeans increasing the number of instances
    - Scaling to demand is generally best done by sxcaling out, as the previous configuration is known to be working.

# S3 - Simple Storage Service & AWS CLI
### Quick intro to AWS CLI
- AWS CLI is the command line interface for AWS. This exists as an alternative to the traditional interface
    - Available through the AWS developer's toolkit. Can be installed and used on any EC2 instance.
    - Allows doing CRUD operations without needing to go through the multi-step AWS console.


**To create AWS CLI**
- Require dependencies: python 3 and above
- AWS access and secret keys
- S3 access through our IAM role
- Note: It is also necessary to create an alias for python3 as python.
## S3 buckets
- These are simply storage for objects. Used to store and protect data
- Highly scalable and usable for any amount of data
- Very useful in disaster recovery planning
- Also allows data to be persistent after an EC2 instance is deleted

Once requirements are installed, need to set up key. This is done by typing `aws configure`, after which the programme prompts the input of authentication details and desired output.

Test connection by typing `aws s3 ls` to list the S3 directories. This will nto work if the permissions were entered incorrectly.

### S3 Storage Classes
- Standard
    - Slightly more expensive than Glacier, but useful for data that is frequently accessed as access time is shorter
- S3 Glacier
    - Useful if something does not need to be available straight away.
    - An example might be old employee records, which you are required to keep but would only be required in response to referencing queries

### CRUD in buckets (huehuehue)

- Now that we have discussed how to authenticate, it is time to try out CRUD operations with buckets

- To make a new bucket, use `aws s3 mb s3://devops-bootcamp-kwolff` (mb for make bucket, the last part is an addres and variable)
    - note that bucket naming convention will not accept underscores, but will accept dashes
    - to explicitily force a particular region, use `-- region [region name]`. Otherwise, the default region given during the authentication step will be used.

- Next, let's create a file called `test.md` on our instance and try to store it in the S3 bucket.
    - To do this, use `aws s3 cp [file or filepath] s3://devops-bootcamp-kwolff`. Of course, the filepath for the s3 bucket changes d3epending on the intended bucket name
- The file copied into the s3 bucket is completely separate from the one on the ec2 instance, so editing or deleting it on the instance will not affect the backed up file.
- Retrieval of file works the same way as copying files to the S3 bucket, but with the first filepath being the S3 address, and the second being destination filepath.

- Can also synchronise folders, using `aws s3 sync [s3 filepath] [localhost folder name]`. If this file is not yet existing on the instance, it will be created

- Deletion of filess uses `aws s3 rm [bucket-path]`
- to delete a whole bucket, we need to use `aws s3 rb [bucket path] [bucket name]`
- to empy a bucket or a folder in a bucket (keep the name but delete all files), use `aws s3 rm [bucket path] --recursive [bucket name]`
    - note that hte bucket name needs to be there to confirm the choice, similar to a `-y` in some linux commands

### AWS CLI

Manipulating S3 buckets is only one of the features of the AWS command line itnerface. It is possible to create any aws resource as they are required. this leads neatly onto infrastructure-as-code.

## boto3

This is a python package that can deal with AWS CLI commands, allowign them to be tied into python programs easily. A few sample functions that can be used to apply CRUD operations to s3 buckets can be seen in the `Boto3_python` directory.

# Autoscaling & Load Balancing

- Autsocaling is the automatic adjustment of comptuational resources based on server load. 
- Load Balancing ditributes traffic between instances to avoid one EC2 instance getting overwhelmed. Done by an Application Load Balancer (ALB) 

### Autoscaling group Exercise:

- Want an autoscalign group that has a minimum size, can scale out as needed (to a maximum size), and scale back down when no longer needed
    - for now, start with 2 EC2 instances and scale out to 3 if required.

**To create and autoscaling group**
```
- Need an ASG: Dependent on having either a launch tmeplate or launch configuration available.
- ALB: Target group HTTP 80, 
- AWS keys
- VPC - Subnets - Security Group


To make highly available, want to deploy in mutliple availability zones, for example first instance in Ireland, second in London, third in Paris. Ideally keep latency low by not having availability zones spaced too far apart.

Info Needed:
- AMI-id
- EBS storage

```

To launch an autoscaling group, need to start with an AMI. This will then be used to set up a launch template:

```
- Select 'Launch Template'
- Select the AMI that you want to use, and configure the other options to fit your.
- under "user Data", it is advisable to add a bash script with the actions required to bring the app online.
- Add the security group of choice and so on, similar to an instance launch

```

With the launch template set up, we can use this to configure an autoscaling group

```