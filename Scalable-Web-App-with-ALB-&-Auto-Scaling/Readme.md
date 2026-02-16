## Scalable Web App with ALB & Auto Scaling – Use EC2, ALB, and Auto Scaling for high availability.

### This project involves deploying a highly available web application using Amazon EC2, Application Load Balancer (ALB), and Auto Scaling. The goal is to ensure that the web app can scale automatically based on traffic demand while maintaining availability.

#### Steps to Deploy a Scalable Web App

### Step 1: Create a Launch Template

1. Go to AWS Console -> EC2 -> Launch Templates → Create Launch Template.

2. Configure:
- Name: WebAppTemplate
- AMI: Amazon Linux 2 or Ubuntu
- Instance Type: t2.micro (Free Tier) or t3.medium
- Key Pair: Select an existing key pair or create a new one.
- Security Group: Allow SSH (22), HTTP (80), and HTTPS (443).


![alt text](demo-images-1602/launch-template-example-part1.png)

![alt text](demo-images-1602/launch-template-example-part2.png)

![alt text](demo-images-1602/launch-template-example-part3.png)


User Data (Optional, for auto-installation of Nginx):
```
#!/bin/bash

sudo apt-get update -y && sudo apt-get install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx

echo "<h1>Welcome to Scalable Web App</h1>" | sudo tee /var/www/html/index.html
```

3. Click Create Launch Template.

### Step 2: Create an Auto Scaling Group

1. Go to EC2 -> Auto Scaling Groups -> Create Auto Scaling Group.

![alt text](demo-images-1602/asg-part1.png)

2. Choose Launch Template: Select WebAppTemplate.

![alt text](demo-images-1602/asg-part2.png)


3. Attach New Load Balancer:
- Choose Application Load Balancer (ALB).
- Create Target Group:
- Target Type: Instance
- Protocol: HTTP
- Health Check: /
- Register instances later (Auto Scaling will handle this).

![alt text](demo-images-1602/asg-attach-new-alb-part3.png)

4. Configure Group Size:
- Desired Capacity: 2
- Minimum Instances: 1
- Maximum Instances: 3

5. Select Network:
- Choose an existing VPC.
- Select at least two subnets across different Availability Zones (AZs).

![alt text](demo-images-1602/asg-part4.png)


6. Set Scaling Policies (Optional):
- Enable Auto Scaling based on CPU utilization.
- Example Policy: Scale out when CPU > 60%, Scale in when CPU < 40%.

![alt text](demo-images-1602/review-asg-creation.png)

7. Click Create Auto Scaling Group.

### Step 3: Create an Application Load Balancer (ALB) if not created during the ASG creation time

1. Go to EC2 > Load Balancers -> Create Load Balancer.

2. Select Application Load Balancer.

3. Configure Basic Settings:
- Name: WebApp-alb
- Scheme: Internet-facing
- VPC: Select the same VPC as Auto Scaling.
- Availability Zones: Choose at least 2.

4. Configure Listeners:
- Protocol: HTTP
- Port: 80

5. Target Group:
- Select existing Target Group (from Auto Scaling Group).

![alt text](demo-images-1602/tg-grp.png)

6. Security Group:
- Allow HTTP (80).

7. Click Create Load Balancer.

![alt text](demo-images-1602/alb-example.png)

### Step 4: Test the Setup

1. Get ALB DNS Name:
- Go to EC2 -> Load Balancers -> Copy the ALB DNS Name.

Open a browser and enter:

```
http://your-alb-dns-name
```
You should see:
Welcome to Scalable Web App

![alt text](demo-images-1602/alb-dns-hit-on-browser.png)

### Step 5: Verify Auto Scaling

1. Stop an Instance:

- Manually stop an instance to verify if Auto Scaling replaces it.

![alt text](demo-images-1602/terminate-ec2-manually-to-verify-asg-creates-new.png)

![alt text](demo-images-1602/new-instance-available-after-manual-termination-of-one-ec2.png)

Conclusion

This setup ensures: 
- High Availability using ALB
- Scalability using Auto Scaling
- Redundancy across multiple AZs

*This architecture is commonly used for production applications that need reliability and cost optimization.*