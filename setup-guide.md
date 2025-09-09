Setup Guide – High Availability & Disaster Recovery
=

🏗️ Step 1: Create VPCs in Two Regions
=
Go to VPC Dashboard.

Create VPC-1 in Region A (e.g., ap-south-1 – Mumbai).

     o	CIDR block: 10.0.0.0/16
     o	Create 2 public subnets in different AZs.
Create VPC-2 in Region B (e.g., us-east-1 – N. Virginia).

    o	CIDR block: 10.0.0.0/16
    o	Create 2 public subnets in different AZs.
    =

 ⚙️ Step 2: Launch EC2 Instances & Auto Scaling
 =
Go to EC2 Dashboard in Region A.

Create a Launch Template with:

    o	Amazon Linux 2 AMI
    o	Instance type: t2.micro 
    o	Security group: allow HTTP (80), HTTPS (443), SSH (22).
    o	User Data (optional for web app):
    o	#!/bin/bash
    o	yum install -y httpd
                       echo "Hello from Mumbai Regions" > /var/www/html/index.html
    o	systemctl start httpd --now
Create an Auto Scaling Group (ASG) using this launch template.

    o	Attach ASG to 2 subnets in Region A.
    o	Min size = 1, Desired = 2, Max = 4.
Repeat steps in Region B, but update User Data: echo "Hello from singapore region" > /var/www/html/index.html
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/9235b934dfffa36d9f78647d725c4aad56def1c7/img/EC2-mumbai.png)
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/72ae5aa42b84dd80eba636b6abf1026d46de768d/img/lt.png)

