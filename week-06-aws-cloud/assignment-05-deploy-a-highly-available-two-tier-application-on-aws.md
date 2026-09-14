# Assignment 5 — Deploy a Highly Available Two-Tier Application on AWS (VPC + ALB + ASG + Multi-AZ RDS)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will design and deploy a highly available two-tier web application on AWS: highly available networking across two Availability Zones, an Application Load Balancer, an Auto Scaling Group for the web tier, and a private Multi-AZ RDS database. You must prove high availability with real failure tests.

---

# Task 1 — Create HA Networking (VPC + 4 Subnets + IGW + NAT + Route Tables)

## Goal

Build a VPC (10.0.0.0/16) with two public and two private subnets across two Availability Zones, an Internet Gateway, a NAT Gateway, and the matching public/private route tables.

### Evidence

#### Screenshot 1 — VPC details showing CIDR 10.0.0.0/16

![alt text](<ha VPC details showing CIDR .png>)


#### Screenshot 2 — Subnets list showing four subnets and their Availability Zones


![alt text](<Subnets list showing four subnets and their Availability Zones.png>)


#### Screenshot 3 — Public route table showing the Internet Gateway route and both public-subnet associations


![alt text](<Public route table showing the Internet Gateway route and both public-subnet associations.png>)


#### Screenshot 4 — Private route table showing the NAT Gateway route and both private-subnet associations

![alt text](<Private route table showing the NAT Gateway route and both private-subnet associations.png>)


#### Screenshot 5 — NAT Gateway status showing Available and the Elastic IP

![alt text](<NAT Gateway status showing Available and the Elastic IP.png>)


# Task 2 — Create Security Groups (ALB, EC2, RDS) with Least Privilege

## Goal

Create `ha-alb-sg` (HTTP public), `ha-web-sg` (HTTP only from `ha-alb-sg`, SSH from your IP), and `ha-db-sg` (database port only from `ha-web-sg`).

### Evidence

#### Screenshot 6 — ALB Security Group inbound rules


![alt text](<ALB Security Group inbound rules.png>)


#### Screenshot 7 — EC2 Security Group inbound rules showing the ALB Security Group reference and SSH from your IP


![alt text](<EC2 Security Group inbound rules showing the ALB Security Group reference and SSH from your IP.png>)


#### Screenshot 8 — RDS Security Group inbound rule showing the database port allowed only from the EC2 Security Group

![alt text](<RDS Security Group inbound rule showing the database port allowed only from the EC2 Security Group.png>)


# Task 3 — Deploy Database Tier (RDS Multi-AZ in Private Subnets)

## Goal

Launch a private, Multi-AZ RDS database (MySQL or PostgreSQL) using the private DB Subnet Group and `ha-db-sg`.

### Evidence

#### Screenshot 9 — RDS summary showing Multi-AZ = Yes and Publicly accessible = No

![alt text](<RDS summary showing Multi-AZ = Yes and Publicly accessible = No.png>)


#### Screenshot 10 — RDS connectivity section showing the DB Subnet Group and Security Group

![alt text](<RDS connectivity section showing the DB Subnet Group and Security Group.png>)


# Task 4 — Build a Launch Template (User Data Installs App + Connects to DB)

## Goal

Create a Launch Template whose user data installs the web-server runtime, deploys the application, configures the database connection, and starts the required services.

### Evidence

#### Screenshot 11 — Launch Template details showing that user data exists, including a visible snippet

![alt text](<Launch Template details showing that user data exists, including a visible snippet.png>)


#### Screenshot 12 — A running instance created from the template showing that the application responds on port 80 through a local test or browser using its public IP


![alt text](<A running instance created from the template showing that the application responds.png>)


# Task 5 — Create an Application Load Balancer (ALB) Across 2 Public Subnets

## Goal

Create an internet-facing ALB across both public subnets with an HTTP listener and a healthy instance target group.

### Evidence

#### Screenshot 13 — ALB details showing two public subnets in two Availability Zones


![alt text](<ALB details showing two public subnets in two Availability Zones.png>)


#### Screenshot 14 — Target group showing at least one healthy target


![alt text](<Target group showing at least one healthy target.png>)


# Task 6 — Create Auto Scaling Group (ASG) in 2 Public Subnets

## Goal

Create an Auto Scaling Group from the Launch Template across both public subnets, with desired capacity 2, minimum 2, and maximum 4, registered to the ALB target group.

### Evidence

#### Screenshot 15 — Auto Scaling Group showing desired, minimum, and maximum capacity and the selected subnet Availability Zones


![alt text](<Auto Scaling Group showing desired, minimum, and maximum capacity and the selected subnet Availability Zones.png>)


#### Screenshot 16 — EC2 instances list showing two running instances in different Availability Zones


![alt text](<EC2 instances list showing two running instances in different Availability Zones.png>)


# Task 7 — Configure App to Use RDS + Validate Read/Write

## Goal

Confirm the application communicates with the RDS database through the ALB DNS name with at least one read and one write operation.

### Evidence

#### Screenshot 17 — Browser showing the application loaded through the ALB DNS name with the URL visible 


![alt text](<browser showing the application loaded through the ALB DNS name with the URL visible.png>)



#### Screenshot 18 — Proof of a database write through a UI message or database query output


![alt text](<Proof of a database write through a UI message or database query output.png>)


# Task 8 — High Availability Tests (Must Do Both)

## Goal

Test A: terminate one web instance and confirm the Auto Scaling Group replaces it automatically without interrupting the ALB.

Test B: simulate an Availability Zone impact (stop, detach, or reduce desired capacity in one AZ) and confirm the application stays available.

### Evidence

#### Screenshot 19 — EC2 showing the terminated instance and the newly launched instance; timestamps are helpful


![alt text](<EC2 showing the terminated instance and the newly launched instance; timestamps are helpful.png>)


#### Screenshot 20 — Target group showing healthy targets after replacement


![alt text](<Target group showing healthy targets after replacement.png>)


#### Screenshot 21 — Evidence that an instance was removed, detached, placed in Standby, or stopped in one Availability Zone


![alt text](<Evidence that an instance was removed, detached, placed in Standby, or stopped in one Availability Zone.png>)


#### Screenshot 22 — Browser showing that the ALB DNS endpoint still works during the change


![alt text](<Browser showing the application loaded through the ALB DNS name.png>)


# Task 9 — Architecture and Test-Results Summary

## Goal

Summarize the VPC/subnet layout, the ALB and Auto Scaling Group setup, the private Multi-AZ RDS setup, and the results of both high-availability tests.

### Evidence

#### Screenshot 23 — A simple architecture diagram, which may be hand-drawn, or an AWS console overview showing the components


![alt text](<A simple architecture diagram, which may be hand-drawn, or an AWS console overview showing the components.png>)

### Notes

Summarize the VPC and subnets across the two Availability Zones.

1. VPC and Subnet Layout

A VPC (vpc-0a25f218843049b5b) was configured in the AWS us-east-1 Region with subnets distributed across two Availability Zones: us-east-1a and us-east-1b. The web application infrastructure was deployed across both Availability Zones to provide high availability, fault tolerance, and protection against a single point of failure.


Summarize the ALB and Auto Scaling Group setup.

ALB and Auto Scaling Group Setup

An internet-facing Application Load Balancer (ha-alb) was configured with an HTTP listener on port 80 and forwards incoming traffic to the ha-web-tg target group. The Auto Scaling Group deploys WordPress EC2 instances across both Availability Zones and automatically registers them with the target group.

The ALB continuously performs health checks and routes traffic only to healthy instances. The Auto Scaling Group maintains the desired number of running instances and can replace unhealthy or failed instances, improving the application's availability and resilience.


Summarize the private Multi-AZ RDS setup.

A private Amazon RDS MySQL database was configured for the WordPress application using a Multi-AZ deployment. The database is not directly accessible from the public internet and is accessible only by the application instances through MySQL port 3306.

The Multi-AZ configuration maintains a standby database instance in a separate Availability Zone, providing automatic failover and improved database availability in the event of an infrastructure failure.

Summarize the results of both high-availability tests.

Both high-availability tests demonstrated that the architecture can tolerate infrastructure failures while maintaining application availability.

During the application-instance failure test, the ALB health checks detected the unhealthy instance and stopped routing traffic to it. The Auto Scaling Group then maintained the required capacity by replacing the failed instance, allowing traffic to continue through healthy instances.

The Multi-AZ architecture also demonstrated resilience against Availability Zone-level failures by distributing resources across multiple AZs. The RDS Multi-AZ configuration provides database failover capability, while the ALB and Auto Scaling Group help keep the web application available despite instance or AZ failures.

# LinkedIn Post (Required)

## Goal

Publish a LinkedIn post about the high-availability build, including the ALB URL (or a redacted screenshot), three to five lines on what you built and how you tested high availability, and one proof screenshot.

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/felix-nwobodo-2a191856_aws-cloudcomputing-devops-activity-7503452477220950017-Xomj?utm_source=social_share_send&utm_medium=member_desktop_web&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE


#### Screenshot of LinkedIn post

![alt text](<Linkedin post for ha deployment assignment 5.png>)


# Submission Instructions

- Add all required screenshots in your submission
- Do not expose passwords, connection strings, private keys, or account IDs

---

# Completion Checklist

- [ ] Task 1: VPC, four subnets, IGW, NAT Gateway, and route tables created (Screenshots 1–5)
- [ ] Task 2: Least-privilege ALB, EC2, and RDS security groups created (Screenshots 6–8)
- [ ] Task 3: Private Multi-AZ RDS created (Screenshots 9–10)
- [ ] Task 4: Self-configuring Launch Template created and tested (Screenshots 11–12)
- [ ] Task 5: ALB created across both public subnets (Screenshots 13–14)
- [ ] Task 6: Auto Scaling Group running two instances across two AZs (Screenshots 15–16)
- [ ] Task 7: Application verified through the ALB with a database read and write (Screenshots 17–18)
- [ ] Task 8: Both high-availability tests completed (Screenshots 19–22)
- [ ] Task 9: Architecture and test-results summary completed (Screenshot 23 & Notes)
- [ ] LinkedIn post published and URL submitted
- [ ] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*