# Cloud Computing
## Amazon Web Services (AWS) Infrastructure
- `EC2` Elastic Computer Cloud
- `S3` Simple Storage Service
- `VPC` Virtual Private Network
- `IG` Internet Gateway
- `RT` Route Tables
- `sn` Subnets
- `NALs` Network Access Control
- `SG` Security Groups
- `CW` Cloud Watch
- `SNS` Simple Notification Service
- `SQS` Simple Queue Service
- `LB`, `ALB`, `ELB`, `NLB` Load Balancers
- `ASG` Auto-scaling Group
- `AMI` Amazon Machine Image
### AWS Regions
### Avaialibility Zones
### Advantages of cloud and AWS
- Scalability
- Accessibility
- Cost effective
- Flexibility
- Efficiency
### Public Cloud
### Private Cloud
### Hybrid cloud
- IaaS (Infrastructure As a Service)
- PaaS (Platform)
- Saas (software)

### Top 3 cloud providers, and the services they provide
- AWS (Amazon Web Services)
  - Global cloud-based products including compute, storage, databases, analytics, networking, mobile, developer tools, management tools, IoT, security and enterprise applications. These services help organizations move faster, lower IT costs, and scale (AWS, 2021)
- Microsoft Azure
  - Building, testing, deploying, and managing applications and services through Microsoft-managed data centers (wiki, 2020).
- Google Cloud Platform
  - Provides a series of modular cloud services including computing, data storage, data analytics and machine learning (Google Cloud, 2021).

### Launch EC2 Instance
#### Entered in Git Bash as admin
- cd ~/.ssh (go into the ssh folder)
- nano key.pem (creates file in ssh folder, paste key contents)
- chmod 400 key.pem (edit permissions)
- execute AWS command (ssh -i...)

### Next Steps
- update and upgrade
- install nginx
- Enable nginx
- Check public ip globally
- install node, correct version
- install required dependencies
- `app code` currently available on `localhost`
- npm install
- npm start


### Step by step
- aws EC2 instance
- Ubuntu Server 16.04 LTS (HVM), SSD Volume Type (free tier)
- t2-micro
- change nothing in part 3
- no new storage
- add 27017 to sg
- launch and connect into terminal with ssh key
  
#### Edit App
- sudo nano ~/.bashrc
- sudo echo 'export DB_HOST="mongodb://IP:27017/posts"' >> .bashrc
- source ~/.bashrc
- sudo systemctl restart nginx
- sudo systemctl enable nginx

#### Launch DB
- sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927
- echo "deb https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
- sudo apt-get update -y
- sudo apt-get upgrade -y
- sudo apt-get install -y mongodb-org=3.2.20 mongodb-org-server=3.2.20 mongodb-org-shell=3.2.20 mongodb-org-mongos=3.2.20 mongodb-org-tools=3.2.20
- cd /etc/
- sudo nano mongod.conf OR sudo touch mongod.conf (add contents(devops_bootcamp_intro)) 
- sudo systemctl restart mongod
- sudo systemctl enable mongod
- sudo systemctl status mongod

#### Final Steps
- cd in app file
- node seeds/seed.js
- npm install
- npm start