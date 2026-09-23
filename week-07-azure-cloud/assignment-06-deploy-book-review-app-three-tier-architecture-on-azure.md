# Assignment 6 — Capstone: Deploy Book Review App (Three-Tier Architecture) on Azure

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

This is the most important assignment of the course. You will deploy the Book Review App in a production-ready, best-practice-compliant three-tier architecture on Azure: separated presentation, application, and database tiers, least-privilege network access, a controlled public entry point, protected secrets, and availability/monitoring evidence.

---

# Task 1 — Design the Azure Three-Tier Architecture

## Goal

Create an architecture diagram and implementation plan identifying the presentation, application, and database components, the chosen Azure services, the public entry point, and the internal traffic paths.

### Evidence

#### Screenshot 1 — Architecture diagram showing the public entry point, three tiers, network boundaries, and traffic flow

![alt text](<Architecture diagram showing the public entry point, three tiers, network boundaries, and traffic flow.png>)


#### Screenshot 2 — Written architecture assumptions and selected Azure services

Written Architecture Assumptions and Selected Azure Services
Architecture Assumptions

The Book Review application is designed as a three-tier architecture consisting of a presentation/web tier, an application/API tier, and a database tier. The deployment is hosted in the South Africa North Azure region.

The web tier is the public entry point for users. It hosts the Next.js frontend and Nginx and is accessed through an Azure public Load Balancer. The application tier runs the Node.js/Express backend on a private virtual machine and communicates with the web tier over the internal Azure network. The database tier uses Azure Database for MySQL and is separated from the application and web tiers to reduce direct exposure.

Only the web tier is intended to be publicly accessible. The application and database tiers use private networking and controlled inbound access. Communication between the backend and database is protected using SSL/TLS. Application secrets and database credentials are stored in environment/secret-management configuration and are not exposed in source code or screenshots.

The architecture assumes that the application requires basic availability monitoring, health checking, database backup and recovery, and controlled network access. Azure Load Balancer health probing is used to verify that the web tier is available before directing traffic to it.

Selected Azure Services
Azure Service	                   Purpose
Azure Virtual Network (VNet)	   Provides the private network boundary for the three-tier application.
Azure Virtual Machines	           Hosts the Next.js/Nginx web tier and Node.js/Express application tier.
Azure Load Balancer	               Provides the public entry point and distributes incoming HTTP traffic to the web tier.
Azure Load Balancer Health Probe	Checks the availability of the Web VM on TCP port 80.
Azure Database for MySQL	        Provides the managed relational database for the Book Review application.
Network Security Groups (NSGs)  	Control inbound and outbound traffic between the different tiers.
Azure Monitor	                   Provides infrastructure monitoring, metrics and operational visibility.
Azure Backup/Database Backup	   Provides database recovery capability through  Azure-managed backup and retention features.
Azure Key Vault / approved secret management	Provides a secure location for application secrets and credentials where configured.
Nginx	                           Acts as the web server and reverse proxy between the public frontend and backend API.
PM2                       	Keeps the Node.js backend running and provides process-level monitoring and management.



# Task 2 — Create the Azure Network Foundation

## Goal

Create a dedicated Resource Group and VNet with separate subnets for the web, application, and database tiers, keeping the application and database tiers without direct public access.

### Evidence

#### Screenshot 3 — Resource Group overview showing the assignment resources


![alt text](<Resource Group overview showing the assignment resources.png>)


#### Screenshot 4 — VNet overview showing the address space and all required subnets

![alt text](<VNet overview showing the address space and all required subnets for blog.png>)


![alt text](<VNet overview showing the address space and all required subnets.png>)


#### Screenshot 5 — Route-table or Private DNS evidence where applicable


![alt text](<Route-table or Private DNS evidence where applicable.png>)


# Task 3 — Configure Security and Secret Management

## Goal

Apply least-privilege NSG rules so traffic flows Internet → public entry point → web tier → application tier → database tier, and store credentials in Azure Key Vault or another approved secure mechanism.

### Evidence

#### Screenshot 6 — NSG rules proving least-privilege access between the tiers


![alt text](<NSG rules proving least-privilege access between the tiers.png>)


#### Screenshot 7 — Key Vault or approved secret-management configuration (without displaying secret values)

![alt text](<Key Vault or approved secret-management configuration (without displaying secret values).png>)


# Task 4 — Deploy the Presentation (Web) Tier

## Goal

Deploy the Book Review App presentation layer on the approved web-tier compute service, configured to route requests to the internal application-tier endpoint, and not directly exposed except through the public entry service.

### Evidence

#### Screenshot 8 — Web-tier compute overview showing subnet and availability configuration


![alt text](<Web-tier compute overview showing subnet and availability configuration.png>)


#### Screenshot 9 — Terminal or service output proving the presentation layer is running


![alt text](<Terminal or service output proving the presentation layer is running.png>)


# Task 5 — Deploy the Business (Application) Tier

## Goal

Deploy the Book Review App backend privately in the application subnet, configured to use the private database endpoint and secured environment values, reachable only through its internal endpoint.

### Evidence

#### Screenshot 10 — Application-tier compute overview showing private subnet placement

![alt text](<Application-tier compute overview showing private subnet placement1.png>)


#### Screenshot 11 — Backend process, service, or listening-port evidence

![alt text](<Backend process, service, or listening-port evidence.png>)

![alt text](<pm2 for persistence.png>)


#### Screenshot 12 — Internal health-check or API response (without exposing secrets)


![alt text](<Internal health-check or API response (without exposing secrets).png>)


# Task 6 — Deploy the Managed Database Tier

## Goal

Create a private Azure managed database (public access disabled), with availability/backup/retention settings, the Book Review App schema imported, and access restricted to the application tier only.

### Evidence

#### Screenshot 13 — Database overview showing private connectivity and public access disabled


![alt text](<Database overview showing private connectivity and public access disabled.png>)

#### Screenshot 14 — Availability, backup, and retention configuration


![alt text](<Availability, backup, and retention configuration.png>)


#### Screenshot 15 — Successful schema or connectivity verification (without exposing credentials)

![alt text](<Successful schema or connectivity verification (without exposing credentials).png>)


# Task 7 — Configure Traffic Management, Availability, and Monitoring

## Goal

Configure the approved public entry service with health probes and backend pools, internal routing for the application tier where required, and enable Azure Monitor/diagnostics/logs/alerts for the key resources.

### Evidence

#### Screenshot 16 — Public entry service showing listener, frontend endpoint, and healthy web targets


![alt text](<Public entry service showing listener, frontend endpoint, and healthy web targets.png>)



#### Screenshot 17 — Internal application-tier load-balancing or routing configuration where applicable


![alt text](<Internal application-tier load-balancing or routing configuration where applicable.png>)


#### Screenshot 18 — Azure Monitor, diagnostic settings, logs, metrics, or alert evidence


![alt text](<Azure Monitor, diagnostic settings, logs, metrics, or alert evidence.png>)


# Task 8 — Validate the Production-Style Deployment

## Goal

Confirm the Book Review App works end to end through the public endpoint, with at least one database read and one write, confirm private tiers are not internet-reachable, and complete a safe availability test.

### Evidence

#### Screenshot 19 — Browser showing the Book Review App through the public endpoint


![alt text](<Browser showing the Book Review App through the public endpoint.png>)


#### Screenshot 20 — Proof of successful database-backed read and write operations

![alt text](<Proof of successful database-backed read and write operations.png>)



#### Screenshot 21 — Evidence that private tiers are not publicly accessible


![alt text](<Evidence that private tiers are not publicly accessible.png>)

![alt text](<Evidence that private tiers are not publicly accessible 1.png>)


#### Screenshot 22 — Availability-test and healthy-target evidence

![alt text](<Availability-test and healthy-target evidence.png>)


#### Public Endpoint

Paste your public endpoint URL here:

http://4.221.211.18/

### Notes

Summarize what worked, issues encountered and how they were fixed, and the availability/security/secrets/monitoring/backup choices made.

The Azure Book Review application was successfully deployed as a three-tier architecture in the South Africa North region. The web tier is exposed through a public Azure Load Balancer, while the application and database tiers remain on private network paths. The frontend runs with Next.js and Nginx, the backend uses Node.js/Express with PM2, and Azure Database for MySQL provides the database layer.

Issues encountered and fixes:

Backend/database connectivity: The application initially had connectivity/configuration issues. The database configuration was corrected, and the backend subsequently confirmed a successful SSL connection to book_review_db.
Backend availability: The Express API was verified with curl, successfully returning the books data from /api/books.
CORS/registration failure: Registration requests were rejected because ALLOWED_ORIGINS contained the placeholder <Public-LB-IP>. It was corrected to the actual public endpoint:
http://4.221.211.18,http://localhost:3000, followed by a PM2 restart.
Frontend/API integration: The frontend was configured to use /api, allowing requests through the public web endpoint while Nginx handles reverse-proxy routing to the backend.

Availability choices:

Public Azure Load Balancer provides the web entry point.
A health probe checks the Web VM on TCP port 80.
The backend application runs on the private App VM on port 3001.
The database is separated from the public web tier.

Security choices:

Private application/database tiers are not intended to have direct public Internet access.
Network security rules restrict communication between tiers.
Database communication uses SSL.
Secrets are kept in environment/secret-management configuration rather than exposed in screenshots or source code.

Secrets management:

Application configuration such as database credentials, JWT configuration, and allowed origins is stored outside the application source code in environment configuration.
Secret values are excluded from submitted evidence and screenshots.

Monitoring choices:

PM2 is used to keep the Node.js backend running and provide application/process monitoring.
Azure monitoring/metrics provide infrastructure-level visibility such as VM CPU utilization and health.
Load Balancer health probing provides an additional availability check for the web tier.

Backup and recovery choices:

Azure Database for MySQL backup and retention capabilities are used to support database recovery.
Database availability and backup settings were treated separately from the Load Balancer/web-tier availability configuration.

# Submission Instructions

- Add all required screenshots and links in your submission
- Do not expose passwords, keys, connection strings, or subscription IDs

---

# Completion Checklist

- [ ] Task 1: Architecture diagram and assumptions documented (Screenshots 1–2)
- [ ] Task 2: Network foundation created with isolated tiers (Screenshots 3–5)
- [ ] Task 3: Least-privilege security and secret management configured (Screenshots 6–7)
- [ ] Task 4: Presentation tier deployed (Screenshots 8–9)
- [ ] Task 5: Application tier deployed privately (Screenshots 10–12)
- [ ] Task 6: Managed database tier deployed privately (Screenshots 13–15)
- [ ] Task 7: Public entry, internal routing, and monitoring configured (Screenshots 16–18)
- [ ] Task 8: End-to-end validation and availability test completed (Screenshots 19–22, Public Endpoint, Notes)
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

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*
