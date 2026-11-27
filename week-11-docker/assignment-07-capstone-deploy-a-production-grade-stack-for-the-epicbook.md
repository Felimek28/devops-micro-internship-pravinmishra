# Assignment 7 — Capstone: Deploy a Production-Grade Stack for The EpicBook

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will deploy the EpicBook application as a production-oriented Docker Compose stack on a cloud VM. You will use optimized container images, isolated networks, health checks, persistent MySQL storage, a selected reverse proxy, logging, backup and restore testing, and reliability procedures.

---

# Task 0 — App Discovery and Architecture

## Goal

Review the EpicBook repository and design the intended application architecture.

### Evidence

#### Screenshot 1 — EpicBook Project Structure

Add a terminal screenshot showing the EpicBook project structure after cloning the repository.

![alt text](<EpicBook Project Structure.png>)



#### Screenshot 2 — Architecture Diagram

Add a screenshot of your architecture diagram showing:

- Public user
- Reverse proxy
- Frontend
- Backend
- Database
- Docker networks
- Public and private ports
- Persistent database storage

Add your full name inside the diagram or as a clear caption below it.

![alt text](<theepicbook-capstone architecture Diagram.png>)
---

#### Screenshot 3 — Environment Variables and Ports Document

Add a screenshot showing the contents of:

```text
docs/02-env-and-ports.md
```

It must document environment-variable names, internal ports, persistent-data details, and the health-check method. Do not expose real credentials or values.

![alt text](<Environment Variables and Ports Document.png>)



# Task 1 — Create Production Docker Images

## Goal

Create optimized production images for the EpicBook backend and frontend.

### Evidence

#### Screenshot 4 — Backend Dockerfile

Add a screenshot showing `backend/Dockerfile`, including:

- Dependency stage
- Minimal runtime stage
- Production startup command
- Internal backend port
- Non-root user configuration

![alt text](<Backend Dockerfile for theepicbook-capstone.png>)



#### Screenshot 5 — Frontend Dockerfile

Add a screenshot showing `frontend/Dockerfile`, including:

- Nginx runtime image
- Static frontend files copied to the Nginx web root

![alt text](<Frontend Dockerfile for theepicbook-capstone.png>)



#### Screenshot 6 — Docker Ignore Files

Add a screenshot showing both:

```text
backend/.dockerignore
frontend/.dockerignore
```
![alt text](<Docker Ignore Files.png>)



#### Screenshot 7 — Docker Image Builds and Size Comparison

Add a terminal screenshot showing successful builds of:

- Baseline backend image
- Optimized backend image
- Frontend image

The screenshot must also show the baseline and optimized backend image-size comparison.

![alt text](<Docker Image Builds and Size Comparison.png>)



#### Screenshot 8 — Backend Running as Non-Root User

Add a terminal screenshot showing the optimized backend container running as a non-root user.

![alt text](<Backend Running as Non-Root User.png>)



### Notes

Write a short note covering:

- Baseline and optimized backend image sizes
- The image-size reduction achieved
- One Docker layer-caching optimization used
- The security benefit of running the backend as a non-root user


The baseline single-stage backend Docker image was 363 MB, while the optimized multi-stage backend image was 295 MB. This represents a reduction of 68 MB (18.73%).

One layer-caching optimization used was copying package.json and package-lock.json before copying the application source code. This allows Docker to reuse the dependency installation layer when application source files change but dependencies remain unchanged, reducing build time.

The optimized backend also runs as a non-root user (node, UID 1000). This improves security by limiting the privileges available to the application inside the container and reducing the potential impact of a security breach.



# Task 2 — Create the Docker Compose Stack and Networks

## Goal

Create one Docker Compose stack containing the reverse proxy, frontend, backend, and MySQL database.

### Evidence

#### Screenshot 9 — Docker Compose Services

Add a screenshot showing `docker-compose.yml` with all four services:

```text
reverse-proxy
frontend
backend
database
```

![alt text](<Docker Compose Services.png>)


#### Screenshot 10 — Networks and Named Volume

Add a screenshot showing:

- `front-tier` network
- `back-tier` network
- `db_data` named volume

![alt text](<Networks and Named Volume.png>)



#### Screenshot 11 — Docker Compose Validation

Add a terminal screenshot showing successful Docker Compose validation without exposing environment-variable values or secrets.

![alt text](<Docker Compose Validation.png>)



# Task 3 — Configure Health Checks and Startup Dependencies

## Goal

Configure health checks and ensure services start only after their dependencies are healthy.

### Evidence

#### Screenshot 12 — Backend Health Endpoint

Add a screenshot showing the backend application configuration for the `/health` endpoint.

![alt text](<Backend Health Endpoint.png>)



#### Screenshot 13 — MySQL and Backend Health Checks

Add a screenshot showing `docker-compose.yml` with health checks for MySQL and the backend.

![alt text](<MySQL and Backend Health Checks.png>)



#### Screenshot 14 — Frontend and Reverse-Proxy Health Checks

Add a screenshot showing:

- Frontend health check
- Reverse-proxy health check
- `depends_on` conditions using `service_healthy`

![alt text](<Frontend and Reverse-Proxy Health Checks.png>)



#### Screenshot 15 — Running Healthy Services

Add a terminal screenshot showing Docker Compose service status. The database, backend, frontend, and reverse proxy must be running successfully.

![alt text](<Running Healthy Services.png>)



#### Screenshot 16 — Public Health Endpoint

Add a terminal screenshot showing a successful response from the public application health endpoint through the reverse proxy.

![alt text](<Public Health Endpoint.png>)



#### Screenshot 17 — Health-Check and Startup-Order Document

Add a screenshot showing the contents of:

```text
docs/03-healthchecks-and-depends-on.md
```

Explain the health-check method for each service and the startup dependency order.

![alt text](<Health-Check and Startup-Order Document.png>)

![alt text](<Health-Check and Startup-Order Document 2.png>)



# Task 4 — Configure the Reverse Proxy and Same-Origin Routing

## Goal

Use either Nginx or Traefik as the only public entry point for the EpicBook application.

### Evidence

#### Screenshot 18 — Selected Reverse-Proxy Configuration

Add a screenshot showing the configuration for your selected reverse proxy.

It must show routes for:

- Static frontend assets
- Application pages
- API requests
- Health endpoint

![alt text](<Selected Reverse-Proxy Configuration.png>)



#### Screenshot 19 — Only Reverse Proxy Publishes Port 80

Add a screenshot of `docker-compose.yml` showing that only the `reverse-proxy` service publishes port 80.

![alt text](<Only Reverse Proxy Publishes Port 80.png>)



#### Screenshot 20 — Reverse-Proxy Route Testing

Add a terminal screenshot showing successful requests through the selected reverse proxy to:

- Application page
- One API endpoint
- One static asset
- Health endpoint

![alt text](<Reverse-Proxy Route Testing.png>)



#### Screenshot 21 — EpicBook Application Through Public IP

Add a browser screenshot showing the EpicBook application loaded through the VM public IP address.

Add your full name as a clear caption below the screenshot.

![alt text](<EpicBook Application Through Public IP.png>)



#### Screenshot 22 — Proxy Routing and CORS Document

Add a screenshot showing the contents of:

```text
docs/04-proxy-routing-and-cors.md
```

Explain the proxy routes and state whether CORS was required and why.

![alt text](<Proxy Routing and CORS Document.png>)



# Task 5 — Prove Data Persistence, Backup, and Restore

## Goal

Verify MySQL persistence and perform a controlled backup and restore drill.

### Evidence

#### Screenshot 23 — MySQL Volume Configuration

Add a terminal screenshot showing the `db_data` named volume and its MySQL mount configuration.

![alt text](<MySQL Volume Configuration.png>)



#### Screenshot 24 — Test Data Before Backup

Add a terminal screenshot showing the selected test data before the backup and restore drill.

![alt text](<Test Data Before Backup-1.png>)



#### Screenshot 25 — Successful Backup Creation

Add a terminal screenshot showing successful backup creation and the backup file stored in the host backup directory.

![alt text](<Successful Backup Creation.png>)



#### Screenshot 26 — Controlled Data-Loss Test

Add a terminal screenshot showing that the selected test record was removed during the controlled data-loss test.

![alt text](<Controlled Data-Loss Test.png>)


#### Screenshot 27 — Restore Verification

Add a terminal screenshot showing successful restore and verification that the deleted test record is available again.

![alt text](<Restore Verification.png>)


#### Screenshot 28 — Persistence After Down/Up Cycle

Add a terminal screenshot showing that database data remains available after a non-destructive Docker Compose down/up cycle.

Do not use `docker compose down -v`.

![alt text](<Persistence After Down and  Up Cycle.png>)

![alt text](<Persistence After Down and Up Cycle 2.png>)


#### Screenshot 29 — Persistence and Backup Document

Add a screenshot showing the contents of:

```text
docs/05-persistence-and-backup.md
```

Include the backup plan and restore procedure.

![alt text](<Persistence and Backup Document.png>)


# Task 6 — Configure Logging and Observability

## Goal

Configure useful reverse-proxy and backend logs without exposing sensitive information.

### Evidence

#### Screenshot 30 — Logging Configuration

Add a screenshot showing:

- Configuration for the selected reverse proxy
- Proxy log format
- Docker Compose host log-directory bind mount

![alt text](<Logging Configuration.png>)


#### Screenshot 31 — Persistent Proxy Logs and Backend Logs

Add a terminal screenshot showing:

- Selected reverse-proxy logs available from the host directory after a proxy restart
- Backend logs displayed through Docker Compose

![alt text](<Persistent Proxy Logs and Backend Logs.png>)


### Notes

Write a short note covering:

- The selected reverse proxy
- Host path used for reverse-proxy logs
- How backend logs are viewed
- Whether JSON or standard text logs were used
- Why passwords, tokens, headers, and database connection strings must not appear in logs


1.Selected Reverse Proxy
Nginx was selected as the reverse proxy for The EpicBook application. It handles incoming HTTP requests and routes them to the backend application or frontend static assets.

2.Host Log Directory
Nginx access and error logs are stored on the EC2 host in:

~/theepicbook-capstone/logs/proxy/

The directory is bind-mounted to /var/log/nginx inside the Nginx container. This allows proxy logs to remain available on the host after the container restarts.

3.Backend Application Logs
Backend application logs are written to container stdout and stderr. They can be viewed using:
docker compose --env-file .env logs backend

4.Log Format
Standard timestamped text logs were used instead of JSON. The Nginx access log records the timestamp, client IP, HTTP method, URI, response status, response size and request duration. Nginx error messages are written separately to the error log.

5.Security and Sensitive Information
Passwords, authentication tokens, authorization headers, session identifiers and database connection strings must never appear in application or proxy logs. Exposing these values could allow unauthorized access to the application, database or user accounts.
Logs should contain only the operational information required for troubleshooting, monitoring and security analysis.


# Task 7 — Deploy and Verify the Stack on a Cloud VM

## Goal

Deploy the completed Docker Compose stack on an AWS or Azure VM and verify public access.

### Evidence

#### Screenshot 32 — VM Public IP and Inbound Rules

Add a cloud-console screenshot showing:

- VM public IP address
- SSH port 22 restricted to your IP address
- HTTP port 80 allowed from Anywhere

![alt text](<VM Public IP and Inbound Rules.png>)


#### Screenshot 33 — Cloud VM Stack Verification

Add a VM terminal screenshot showing:

- Docker Compose service status
- Successful public health or API response
- No published database, frontend, or backend ports

![alt text](<Cloud VM Stack Verification.png>)


#### Screenshot 34 — EpicBook Application on Cloud VM

Add a browser screenshot showing the EpicBook application loaded through the VM public IP address.

Add your full name as a clear caption below the screenshot.

![alt text](<EpicBook Application on Cloud VM.png>)


### Notes

Write a short note covering:

- Cloud provider used
- VM operating system
- Public port exposed
- Security rules configured
- Confirmation that the application and backend API worked through the reverse proxy

Cloud provider: Amazon Web Services (AWS) — EC2.
VM operating system: Ubuntu Linux.
Public port exposed: TCP port 80 (HTTP), through the Nginx reverse proxy.
Security rules configured:
  TCP 22 (SSH): Restricted to my IP address.
  TCP 80 (HTTP): Open to public access.
  TCP 3000 and 5000: Not publicly exposed.
  TCP 3306 (MySQL): Not publicly exposed.
Application and API verification: The EpicBook application loaded successfully through the EC2 public IP address. The /health endpoint and /api/cart backend API returned successful HTTP responses through the Nginx reverse proxy.

The frontend, backend, and database communicate internally through Docker Compose networks. Only the reverse proxy is exposed publicly, providing a controlled entry point to the application.


# Task 8 — Automate Deployment with CI/CD (Optional)

## Goal

Optionally automate image build, image push, and deployment through GitHub Actions or Azure Pipelines.

### Optional Evidence

#### Optional Screenshot — Successful CI/CD Pipeline Run

Add a screenshot showing a successful pipeline run with build, image push, deployment, and verification stages.

Optional question was not attempted
---

### Optional Notes

Write a short note covering:

- CI/CD platform used
- Image-tagging method
- Registry used
- Deployment trigger
- Manual approval or secret-handling approach

---

# Task 9 — Perform Reliability Tests and Create an Operations Runbook

## Goal

Test controlled service failures and document safe operating procedures.

### Evidence

#### Screenshot 35 — Backend Failure and Recovery

Add a terminal screenshot showing:

- Backend failure test
- Expected unavailable response through the reverse proxy
- Backend restart
- Successful health-check recovery

![alt text](<Backend Failure and Recovery.png>)


#### Screenshot 36 — Database Failure and Recovery

Add a terminal screenshot showing:

- Database outage test
- Failed database-dependent request
- Database restart
- Successful application recovery

![alt text](<Database Failure and Recovery.png>)

![alt text](<Database Failure and Recovery 2.png>)


### Notes

Write a short operations runbook covering:

- Safe restart procedure for reverse proxy, frontend, backend, and database
- Backup and restore procedure
- Secret-rotation approach
- Database recovery procedure
- What to check when the application returns an error
- Results of backend and database reliability tests

EpicBook Operations Runbook

1.Start and Stop the Stack

Start all services:
docker compose --env-file .env up -d


Stop safely without deleting persistent data:

docker compose --env-file .env down


Never use docker compose down -v.

2.Safe Service Restarts

Restart individual services when necessary:
docker compose --env-file .env restart reverse-proxy
docker compose --env-file .env restart frontend
docker compose --env-file .env restart backend
docker compose --env-file .env restart database

Check service health:
docker compose --env-file .env ps

3.Logs and Troubleshooting

View service logs:
docker compose --env-file .env logs --tail=50

When the application returns an error, check:
Container health and service status.
Nginx access and error logs in logs/proxy/.
Backend and database logs.
Database connectivity and .env configuration.

Test application endpoints:

curl -i http://localhost/health
curl -i http://localhost/api/cart


4.Database Backup and Restore

Create a timestamped backup:
BACKUP_FILE="backups/bookstore-$(date -u +%Y%m%dT%H%M%SZ).sql"
mkdir -p backups
chmod 700 backups

docker compose --env-file .env exec -T database sh -c \
'exec mysqldump -u"$MYSQL_USER" -p"$MYSQL_PASSWORD" "$MYSQL_DATABASE" --single-transaction --no-tablespaces --routines --triggers' \
> "$BACKUP_FILE"

chmod 600 "$BACKUP_FILE"

Restore from a verified backup:
bash
docker compose --env-file .env exec -T database sh -c \
'exec mysql -u"$MYSQL_USER" -p"$MYSQL_PASSWORD" "$MYSQL_DATABASE"' \
< backups/your-verified-backup.sql

Verify the backup before restoring. Protect existing data and never delete the database volume as a recovery method.

5.Secret Rotation
Update database passwords and other credentials in the protected .env file.
Restrict .env permissions.
Restart affected services.
Verify database connectivity and application functionality.
Never commit credentials or private keys to GitHub.

6.Database Recovery

If MySQL becomes unavailable:
docker compose --env-file .env start database
docker compose --env-file .env ps

Wait for MySQL to become healthy. If the backend does not reconnect automatically:
docker compose --env-file .env restart backend

Verify /health and /api/cart.

7.Post-Incident Checklist

All four services are healthy.
/health returns HTTP 200.
/api/cart returns a successful response.
Frontend loads through Nginx.
Logs show no unresolved errors.
Database data and named volumes remain intact.
Backup is available and incidents are documented.


Reliability Test Record

Backend test: Stopped the backend container. Nginx returned HTTP 504. Restarted the backend, which became healthy. /health returned HTTP 200 with the database connected. Result: Successful.

Database test: Stopped MySQL. /api/cart and /health returned HTTP 504. Restarted MySQL, which became healthy; the backend also reported healthy. Final endpoint checks are required to confirm complete application recovery.

Data protection: No named volumes or persistent database data were intentionally deleted during testing.



# Final Public Application URL

**EpicBook URL:** `http://56.228.33.145/`


---

# GitHub Repository URL

**Your Fork or Repository URL:** `https://github.com/Felimek28/theepicbook.git`

---

# LinkedIn Requirement

## Goal

Create a professional LinkedIn post of 6–10 lines about your EpicBook capstone deployment.

Your post must include:

- The architectural decision that most improved reliability
- Your biggest image-size reduction, with numbers
- Key production-hardening lessons
- A deployment verification image

### Evidence

**LinkedIn Post URL:** `https://www.linkedin.com/posts/felix-nwobodo-2a191856_devops-docker-dockercompose-ugcPost-7531436241289093122-Kg-K/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE`

#### LinkedIn Post Screenshot

![alt text](<Linkedin post for epicbook-capstone.png>)

---

# Submission Checklist

- [ ] EpicBook repository reviewed and architecture diagram created
- [ ] Environment variables, ports, persistence, and health-check details documented
- [ ] Backend and frontend production Dockerfiles created
- [ ] Backend runs as a non-root user
- [ ] Docker image-size comparison completed
- [ ] Docker Compose stack includes reverse proxy, frontend, backend, and database
- [ ] `front-tier` and `back-tier` networks configured
- [ ] `db_data` named volume configured
- [ ] MySQL, backend, frontend, and reverse-proxy health checks configured
- [ ] Startup dependencies use `service_healthy`
- [ ] Nginx or Traefik selected as the only public reverse proxy
- [ ] Only reverse-proxy port 80 is publicly published
- [ ] Same-origin routing configured and CORS used only when required
- [ ] Backup, restore, and persistence testing completed
- [ ] Reverse-proxy and backend logs verified
- [ ] Cloud VM deployment verified through the public IP
- [ ] Backend and database reliability tests completed
- [ ] Screenshots 1–36 included
- [ ] Required notes completed
- [ ] LinkedIn post URL and screenshot included
- [ ] Full name visible in required screenshots or captions
- [ ] No passwords, tokens, private keys, account IDs, or other sensitive information exposed

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
