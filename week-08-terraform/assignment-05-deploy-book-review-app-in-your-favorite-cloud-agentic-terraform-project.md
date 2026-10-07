# Capstone Assignment — Deploy the Book Review App Using Terraform and Claude Code Agentic AI

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Student Details

**Full Name:** Felix Emeka Nwobodo 
**Cloud Platform:** AWS 
**GitHub Repository URL:**https://github.com/Felimek28/book-review-terraform-capstone.git
**Public Application URL / Load-Balancer DNS:** public-alb-2062653275.us-east-1.elb.amazonaws.com

---

## Purpose

Deploy the Book Review App using Terraform on AWS or Azure in a secure, highly available, production-style three-tier architecture. Use Claude Code, specialized subagents, Terraform MCP, and validation hooks to support the engineering workflow while keeping all infrastructure-changing operations under human control.

---

# Task 0 — Prepare the Project and Agentic AI Environment

## Goal

Prepare the Book Review App project and configure the provided Claude Code Agentic AI starter kit with project context, specialized subagents, Terraform MCP, validation hooks, and safety guardrails.

## Evidence

### Screenshot 1 — Project `CLAUDE.md`

Add a screenshot of the project `CLAUDE.md` showing the three-tier architecture, security boundaries, Terraform requirements, and human-approval rules.


![alt text](<project CLAUDE.md showing the three-tier architecture, security boundaries, Terraform requirements, and human-approval rules.png>)


### Screenshot 2 — Terraform Engineer Subagent

Add a screenshot showing the Terraform Engineer subagent configuration.

![alt text](<Terraform Engineer subagent configuration..png>)


### Screenshot 3 — Architecture and Security Reviewer Subagent

Add a screenshot showing the Architecture and Security Reviewer subagent configuration.

![alt text](<Architecture and Security Reviewer subagent configuration1.png>)


### Screenshot 4 — Terraform MCP Connection

Add a screenshot showing Terraform MCP connected and available.


![alt text](<Terraform MCP connected and available.png>)


### Screenshot 5 — Validation Hooks

Add a screenshot showing the configured Claude Code validation hooks.

![alt text](<Claude Code validation hooks.png>)


# Task 1 — Design the Three-Tier Architecture

## Goal

Design the required secure, highly available three-tier architecture and create an architecture diagram before building the infrastructure.

The diagram must show:

- VPC or VNet
- Availability Zones or equivalent availability locations
- Six subnets
- Internet connectivity
- NAT or outbound design
- Public load balancer
- Web Tier
- Internal load balancer
- Application Tier
- Managed MySQL
- Read replica
- Main traffic flow

## Architecture Diagram

Add the completed architecture diagram here.

![alt text](<Architecture diagram showing the public entry point, three tiers, network boundaries, and traffic flow.png>)


# Task 2 — Build the Terraform Networking and Security Layers

## Goal

Create the modular Terraform project and implement the network and security layers across the required public and private subnets.

## Evidence

### Screenshot 6 — Modular Terraform Project Structure

Add a screenshot showing the modular Terraform project structure.

![alt text](<modular Terraform project structure..png>)


### Screenshot 7 — Six-Subnet Architecture

Add a screenshot showing the six-subnet architecture across two availability locations.

![alt text](<six-subnet architecture across two availability locations for bookreview.png>)


### Screenshot 8 — Public and Private Tier Separation

Add a screenshot showing the public and private tier separation, including routing and security boundaries.


![alt text](<public and private tier separation, including routing and security boundaries.png>)

# Task 3 — Build the Load-Balancing and Compute Layers

## Goal

Deploy the public and internal load balancers and the Web and Application compute resources required by the Book Review App.

## Evidence

### Screenshot 9 — Web and Application Compute

Add a screenshot showing the Web and Application compute resources in their required subnets.

![alt text](<Web and Application compute resources in their required subnets.png>)


### Screenshot 10 — Public Load Balancer

Add a screenshot showing the internet-facing public load balancer.

![alt text](<internet-facing public load balancer.png>)


### Screenshot 11 — Internal Load Balancer

Add a screenshot showing the private internal load balancer.

![alt text](<private internal load balancer.png>)


### Screenshot 12 — Healthy Targets

Add a screenshot showing healthy target groups or backend pools.


![alt text](<healthy target groups or backend pools..png>)


# Task 4 — Build the Managed MySQL Database Layer

## Goal

Deploy a private, highly available managed MySQL database with a read replica and restrict database connectivity to the Application Tier.

## Evidence

### Screenshot 13 — Managed MySQL Database

Add a screenshot showing the managed MySQL database deployment.


![alt text](<managed MySQL database deployment.png>)


### Screenshot 14 — High Availability

Add a screenshot showing the Multi-AZ or high-availability configuration.

![alt text](<Multi-AZ or high-availability configuration.png>)


### Screenshot 15 — Read Replica

Add a screenshot showing the read replica configuration.

![alt text](<read replica configuration.png>)

### Screenshot 16 — Private Database Access

Add a screenshot showing that the database is private and accepts MySQL traffic only from the Application Tier.

![alt text](<database is private and accepts MySQL traffic only from the Application Tier.png>)


# Task 5 — Validate, Review, and Apply the Terraform Configuration

## Goal

Validate the Terraform configuration, review the execution plan using both Agentic AI and human judgment, and apply the infrastructure changes only after all required checks pass.

## Evidence

### Screenshot 17 — Terraform Validation

Add a screenshot showing successful `terraform validate` output.


![alt text](<terraform validate for bookreview validate.png>)


### Screenshot 18 — Terraform Plan

Add a screenshot showing the Terraform plan output.


![alt text](<terraform plan for bookreview capstone.png>)


### Screenshot 19 — Terraform Apply

Add a screenshot showing successful `terraform apply` completion.

![alt text](<terraform apply output for bookreview.png>)

# Task 6 — Deploy and Configure the Book Review Application

## Goal

Deploy and configure the Book Review App across the Web, Application, and Database tiers and verify the complete application functionality.

## Evidence

### Screenshot 20 — Homepage

Add a screenshot showing the Book Review App homepage through the public endpoint.

![alt text](<Book Review App homepage through the public endpoint.png>)


### Screenshot 21 — Login or Authentication

Add a screenshot showing successful login or authentication.

![alt text](<Successful login or authentication.png>)


### Screenshot 22 — Book Data

Add a screenshotshowing the book listing or book details.


![alt text](<Book list for bookreview capstone.png>)


### Screenshot 23 — Review Functionality

Add a screenshot showing the review functionality working successfully.

![alt text](<review functionality working successfully.png>)


### Screenshot 24 — Backend or API Evidence

Add a screenshot showing that the backend or API is working successfully.

![alt text](<backend or API is working successfully.png>)



### Screenshot 25 — Database Reads and Writes

Add a screenshot showing successful database reads and writes.


![alt text](<book added in the database.png>)

![alt text](<database reads and writes for bookreview.png>)


## Public Application URL

**Public Application URL / DNS:** http://public-alb-2062653275.us-east-1.elb.amazonaws.com/



# Task 7 — Demonstrate the Agentic AI Workflow

## Goal

Demonstrate how Claude Code assisted with Terraform generation, architecture and security review, and evidence-based troubleshooting while infrastructure-changing decisions remained under human control.

You do not need to submit your complete Claude Code conversation history. Include only focused evidence.

## Evidence

### Screenshot 26 — AI-Assisted Terraform Generation

Add a screenshot showing one useful example of AI-assisted Terraform generation or improvement.

![alt text](<AI-assisted Terraform generation or improvement.png>)


### Screenshot 27 — Architecture or Security Review

Add a screenshot showing one structured architecture or security review result.


![alt text](<architecture or security review result.png>)


### Screenshot 28 — AI-Assisted Troubleshooting

Add a screenshot showing one AI-assisted troubleshooting interaction based on collected evidence.


![alt text](<AI-assisted troubleshooting interaction based on collected evidence.png>)


# Task 8 — Complete the Final Architecture Review

## Goal

Review the completed infrastructure against the original capstone requirements and resolve significant architecture, security, reliability, and cost issues.

Confirm that the final review covers:

- Tier separation
- Availability
- Public exposure
- Routing
- Security rules
- Load balancing
- Database privacy
- Secrets
- Terraform quality
- Module structure
- Reliability
- Obvious cost risks

Use Screenshot 27 as the focused evidence for the structured architecture or security review.

---

# Task 9 — Answer the Reflection Questions

## Goal

Reflect on the architecture, Terraform implementation, and Agentic AI workflow. Answer each question briefly in your own words.

## Architecture

### 1. Why did you separate the Web, Application, and Database tiers?

I separated the Web, Application, and Database tiers to enforce separation of responsibilities, security, and maintainability. The Web Tier is responsible for handling client requests and serving the frontend, the Application Tier manages business logic and API requests, while the Database Tier securely stores persistent application data.

This separation allows each tier to be secured and managed independently, limits unnecessary network exposure, and makes the architecture easier to scale, troubleshoot, and maintain. It also ensures that sensitive resources, particularly the database, are not directly exposed to the public internet.



### 2. Why is the Application Tier private?

The Application Tier is kept private because it does not need to be directly accessible from the internet. Client requests are received by the Web Tier and then forwarded to the Application Tier through the Internal Load Balancer.

Keeping the Application Tier private reduces the attack surface and prevents direct internet access to the backend API, including port 3001. This ensures that backend services can only be accessed through the intended application path while allowing security controls such as network rules and load balancing to be applied between the tiers.



### 3. Why is MySQL private?

MySQL is kept private because the database should not be directly accessible from the internet. It only needs to accept connections from the Application Tier, which communicates with it over the private network.

Keeping MySQL private reduces the attack surface, protects sensitive application data, and prevents unauthorized external connections to the database. Access is controlled through network security rules, allowing only the required application traffic while keeping the database isolated from public internet access.


### 4. Why are multiple Availability Zones used?

Multiple Availability Zones are used to improve the application's availability, resilience, and fault tolerance. By distributing resources across separate Availability Zones, the application is less dependent on a single physical location.

If one Availability Zone experiences an infrastructure failure or becomes unavailable, resources in another Availability Zone can continue to serve the application. This reduces the risk of a single point of failure and helps maintain service availability during infrastructure disruptions.



### 5. What is the difference between Multi-AZ/high availability and a read replica?

Multi-AZ/high availability is primarily designed to improve availability and fault tolerance. A standby database is maintained in a separate Availability Zone so that the service can fail over if the primary database becomes unavailable. The standby is generally not used to serve normal application read traffic.

A read replica, on the other hand, is primarily designed to improve read performance and scalability. It maintains a copy of the database that can handle read-only queries, allowing read traffic to be distributed away from the primary database.


## Terraform

### 6. How did you divide your Terraform into modules?

I divided the Terraform configuration into reusable modules based on infrastructure responsibilities. This keeps the code organized, maintainable, and easier to manage.

I created separate modules for:

Networking – manages the VPC, public and private subnets, route tables, Internet Gateway, and NAT Gateway.
Security – manages security groups and controls traffic between the different tiers.
EC2 – provisions and configures the EC2 instances for the Web and Application tiers.
ALB – manages the public and internal Application Load Balancers, target groups, listeners, and health checks.
Database – provisions and manages the RDS MySQL database and its associated configuration.

This modular approach allows each component to be developed, tested, reused, and updated independently, while keeping the root Terraform configuration clean and easier to understand.


### 7. How do the modules communicate through variables and outputs?

The modules communicate through input variables and outputs. The root Terraform configuration passes required values into each module using variables, while modules expose important resource information through outputs.


### 8. What did you specifically check in `terraform plan`?


In terraform plan, I reviewed the proposed infrastructure changes before applying them. I specifically checked that:

The correct resources and number of instances would be created.
Resources were being deployed in the correct VPC, subnets, and Availability Zones.
The security groups and network rules allowed only the required traffic between the tiers.
The public and internal load balancers, target groups, listeners, and health checks were configured correctly.
The RDS MySQL database was configured as intended and not exposed publicly.
Resource dependencies and references between modules were correct.
Terraform was not planning to unexpectedly destroy or modify existing resources.


## Agentic AI

### 9. What was the purpose of `CLAUDE.md`?

CLAUDE.md served as a project-specific instruction and context file for Claude Code. It provided Claude with information about the project, including the infrastructure architecture, coding conventions, Terraform structure, security requirements, and important deployment guidelines.

This helped Claude understand the project's requirements and generate or modify Terraform configurations more consistently. It also reduced the need to repeatedly provide the same project context and helped ensure that AI-assisted changes followed the intended architecture and best practices.


### 10. What work did the Terraform Engineer subagent perform?

The Terraform Engineer subagent reviewed the Terraform configuration against the capstone requirements to identify configuration gaps and potential improvements.

It inspected the VPC and six-subnet design, routing, EC2 placement, public and internal load balancers, security groups, RDS configuration, Multi-AZ setup, read replica, and relationships between Terraform modules. It also evaluated whether the resources were connected correctly through variables and outputs and provided targeted recommendations to improve the infrastructure where necessary.

The subagent's role was primarily to analyze and validate the Terraform design, while I reviewed its recommendations and made the appropriate changes.



### 11. What did the Architecture and Security Reviewer identify?

The reviewer confirmed that most of the required architecture and security controls were correctly implemented, including tier separation, private application instances, private RDS, security-group chaining, Multi-AZ, and the read replica.

It also identified risks such as a single-AZ NAT Gateway, static EC2 instances without Auto Scaling, local Terraform state, cost exposure from always-on resources, and limited RDS deletion protection.


### 12. Why did you use Terraform MCP instead of relying only on Claude's existing Terraform knowledge?

I used Terraform MCP to give Claude more direct, project-specific access to Terraform information and tooling rather than relying only on its general knowledge.

Terraform MCP helped provide more reliable context about Terraform resources, providers, configurations, and infrastructure operations, which reduced the risk of generating outdated or incorrect configurations. It also allowed Claude to work more closely with the actual Terraform workflow and validate proposed changes against the infrastructure requirements.



### 13. What was the purpose of your validation hooks?

The validation hooks provided automated quality checks around the Terraform workflow. Their purpose was to catch formatting, syntax, and configuration issues early, before changes progressed to the planning or deployment stages.

They supported a controlled workflow:

Generate/Edit → terraform fmt → terraform validate → terraform plan → AI Review → Human Review → terraform apply

### 14. Describe one real issue Claude helped you troubleshoot.

One real issue Claude helped me troubleshoot occurred while deploying the Book Review application on AWS using Terraform. The RDS database creation was failing because I had configured the database to use MySQL 8.0 while specifying the default.mysql8.4 parameter group, which was incompatible with the selected engine version.

I used Claude to analyze the Terraform error message and review the RDS configuration. It identified the version mismatch and recommended changing the parameter group to default.mysql8.0 so that it matched the MySQL engine version.

After making the change, I ran terraform fmt, terraform validate, and terraform plan before applying the configuration again. The database was then created successfully.


### 15. Describe one recommendation you reviewed, modified, or rejected instead of accepting blindly.

During the final architecture review, Claude recommended several production-oriented improvements, including deploying a NAT Gateway in each Availability Zone and replacing the static EC2 instances with Auto Scaling Groups.

I reviewed these recommendations against the specific requirements and scope of my Book Review capstone rather than implementing them automatically. While the recommendations would provide additional resilience and scalability for a production environment, they would also introduce additional infrastructure complexity and increased costs that were not necessary for the assignment.

I therefore kept the simpler architecture that satisfied the capstone requirements. This reinforced an important lesson for me: AI recommendations are engineering input that must be evaluated against requirements, constraints, cost, and architecture—not decisions that should be accepted blindly.



# Task 10 — Publish the Mandatory LinkedIn Post

## Goal

Publish a LinkedIn post describing the capstone, the technical work completed, the Agentic AI workflow, and the lessons learned.

Write the post in your own words, include at least one project image or other proof, and ensure that it can be viewed by the submission reviewer.

## LinkedIn Post URL

**LinkedIn Post URL:**  https://www.linkedin.com/posts/felix-nwobodo-2a191856_aws-terraform-devops-ugcPost-7511866554938994688-OOXS/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE



# Submission Instructions

- Complete Tasks 0–10 in sequence.
- Include all Screenshots 1–28 exactly as specified.
- Ensure that your full name is visible in the required screenshots.
- Include the selected cloud platform.
- Include the completed architecture diagram.
- Include the modular Terraform project structure.
- Include the working public application URL or public load-balancer DNS.
- Include all required Agentic AI workflow evidence.
- Answer all 15 reflection questions briefly in your own words.
- Include the published LinkedIn post URL.
- Do not expose cloud credentials, database passwords, SSH private keys, JWT secrets, access tokens, account IDs, Terraform state containing sensitive values, or other confidential information.
- Review all screenshots and project files carefully before submitting through GitHub.

---

# Completion Checklist

- [ ] Selected AWS or Azure
- [ ] Added and reviewed the Agentic AI starter files
- [ ] Configured `CLAUDE.md`
- [ ] Configured the Terraform Engineer subagent
- [ ] Configured the Architecture and Security Reviewer subagent
- [ ] Connected Terraform MCP
- [ ] Configured validation hooks and safety guardrails
- [ ] Created the architecture diagram
- [ ] Created the six-subnet design
- [ ] Configured public Web Tier routing
- [ ] Kept the Application Tier private
- [ ] Kept the Database Tier private
- [ ] Configured tier-specific Security Groups or NSGs
- [ ] Restricted backend port `3001`
- [ ] Restricted MySQL port `3306` to the Application Tier
- [ ] Created the public load balancer
- [ ] Created the internal load balancer
- [ ] Configured listeners and health checks
- [ ] Deployed the Web Tier compute resources
- [ ] Deployed the private Application Tier compute resources
- [ ] Provisioned private managed MySQL
- [ ] Configured Multi-AZ or high availability
- [ ] Configured a read replica
- [ ] Created the modular Terraform project
- [ ] Used variables, outputs, and module dependencies
- [ ] Used current Terraform documentation through MCP
- [ ] Used hooks for deterministic validation
- [ ] Completed `terraform fmt`
- [ ] Completed `terraform validate`
- [ ] Reviewed `terraform plan`
- [ ] Completed the Terraform Engineer review
- [ ] Completed the Architecture and Security review
- [ ] Applied the infrastructure only after human approval
- [ ] Deployed and configured the backend
- [ ] Deployed and configured the frontend
- [ ] Configured Nginx where required
- [ ] Configured the internal backend endpoint
- [ ] Configured the public frontend endpoint
- [ ] Verified the homepage
- [ ] Verified login or authentication
- [ ] Verified book data
- [ ] Verified review functionality
- [ ] Verified the backend API
- [ ] Verified database reads and writes
- [ ] Verified healthy load-balancer targets
- [ ] Included AI-assisted Terraform generation evidence
- [ ] Included one architecture or security review
- [ ] Included one AI-assisted troubleshooting example
- [ ] Completed the final architecture review
- [ ] Answered all 15 reflection questions
- [ ] Published the mandatory LinkedIn post
- [ ] Added the LinkedIn post URL
- [ ] Captured all 28 required screenshots
- [ ] Confirmed that my full name is visible in the required screenshots
- [ ] Checked that no secrets or sensitive information are exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory), focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations through hands-on experience.

---

## Resources

- Book Review App Repository: [https://github.com/pravinmishraaws/book-review-app](https://github.com/pravinmishraaws/book-review-app)
- DMI Official Website: [https://dmi.pravinmishra.com](https://dmi.pravinmishra.com)
- University: [https://university.pravinmishra.com](https://university.pravinmishra.com)
- Discord Community: [https://discord.pravinmishra.com](https://discord.pravinmishra.com)
- Blog: [https://dmi.pravinmishra.com/blog](https://dmi.pravinmishra.com/blog)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra on LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory on LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of the DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
