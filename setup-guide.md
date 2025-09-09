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
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/1600131494add1e61ca86d75cae6d5576a92ec55/img/ec2-singapore.png)
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/9de00f51ca52d75fdfe5a355a3f4b362d59d0e57/img/slt.png)

Autoscaling Group(ASG)
=
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/3619f8cffab33b7b2d8f2684c64c0ff271910c2b/img/ag-mum.png)
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/c95b62c887aeef10091822b095c7a50e927859d7/img/fag.si.png)
=
🌐 Step 3: Setup Target Group and Load Balancers
=
In Region A, create an Target Group.

      select EC2 instances that you want to target.
In Region A, create an Application Load Balancer (ALB).

    o	Attach ALB to the 2 subnets in different AZs.
    o	Target Group → attach the ASG.
    o	Listener → HTTP (80) forward to Target Group.
Repeat the same steps in Region B.
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/acfaec58d11cbd96f0eaf0fda2a8355ece2018a6/img/tg.mum.png)
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/1d45b39348f3ea2629b885a70b3a2518297f2132/img/elb-mum.png)

🌍 Step 4: Configure Route 53 for Failover
=
Go to Route 53 → Hosted Zones.

Create a new hosted zone (e.g., myhaapp.com).

Add 2 A-records with failover policy:

    o	Record 1 (Primary) → Alias → ALB DNS name (Region A).
    o	Record 2 (Secondary) → Alias → ALB DNS name (Region B).
    o	Set health check for Region A load balancer.
Test DNS:

    o	If Region A is UP → traffic goes to Region A.
    o	If Region A is DOWN → Route 53 sends traffic to Region B.
Hosted Zone:
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/87e6e6cbef1862e07c285646fdd0e32e5b2458ab/img/hosted.zone.png)
Mumbai:
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/8a2d08cbe7645c6d8ab73ead8a06b14ff5d50769/img/hc.mum.png)
singapore:
![image_alt](https://github.com/meghapawar177-droid/High-Availablity-and-Disaster-Recovery-Management-Project/blob/b3c393be1bfba089a29ea6241144c3c36a8b1711/img/hc.singa.png)

🔍 Step 5: Testing Disaster Recovery
=
• Stop all EC2 instances in Region A → Route 53 will failover to Region B.

• Restart Region A instances → traffic will return to primary region.

Mumbai Region:
![image_alt]()
