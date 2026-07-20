# Assignment 3 — Production Maintenance Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will treat your already deployed React application (on Ubuntu VM with Nginx) as a live production system. You will perform structured operational checks covering network validation, service health, log analysis, resource monitoring, configuration verification, and incident simulation with recovery — mirroring real on-call DevOps responsibilities.

---

# Task 1 — Server Access & Networking Validation

## Goal

Verify that the deployed React application is reachable from the browser and confirm basic network connectivity of the Ubuntu VM.

### Evidence

#### Screenshot 1 — Browser showing the React app with your Full Name visible on the UI

![alt text](<Browser showing the deployed React app -1.png>)

#### Screenshot 2 — Output of `ip a`

![alt text](<ip a output.png>)

#### Screenshot 3 — Output of `sudo ss -tulpen`

![alt text](<sudo ss -tulpen-1.png>)


#### Screenshot 4 — Output of `sudo ufw status`

![alt text](<sudo ufw status.png>)

### Notes

Answer the following in your own words:

**1. What proves Nginx is listening on 0.0.0.0:80?**

The output of sudo ss -tulpen provides the proof. The line showing tcp LISTEN with 0.0.0.0:80 and the nginx process confirms that Nginx is listening on port 80. The address 0.0.0.0 indicates that Nginx is bound to all available IPv4 network interfaces, rather than only 127.0.0.1 (localhost). This means it can accept HTTP requests from any reachable IP address, including external clients, provided the firewall and cloud security group allow inbound traffic on port 80. The presence of the nginx process name confirms that Nginx, and not another application, is the service listening on that port.

**2. What proves SSH is active on port 22?**

The sudo ss -tulpen output provides the evidence that SSH is active. The line showing tcp LISTEN with the local address 0.0.0.0:22 and the process sshd confirms that the SSH daemon is listening on port 22. The address 0.0.0.0 indicates that SSH is bound to all available IPv4 network interfaces, allowing the server to accept SSH connections from any reachable IP address. 

**3. Did you find any unexpected open ports? Explain briefly.**

No, I did not find any unexpected open ports. The output shows that only the expected services are listening: SSH (sshd) on port 22 for remote administration, systemd-resolved on port 53 for DNS resolution, chronyd on port 323 for network time synchronization, and systemd-networkd on port 68 for DHCP. There are no unfamiliar or unnecessary services listening on publicly accessible ports. The only externally accessible service shown is SSH on port 22 (0.0.0.0:22), which is expected for managing the EC2 instance
# Task 2 — Service Health & Systemd Validation (Nginx)

## Goal

Verify that Nginx is properly installed, running, enabled at boot, and safely configured.

### Evidence

#### Screenshot 1 — Output of `systemctl status nginx --no-pager`

![alt text](<systemctl status nginx --no-pager.png>)

#### Screenshot 2 — Output of `sudo nginx -t`

![alt text](<sudo nginx -t.png>)

#### Screenshot 3 — Output of `sudo ss -lptn '( sport = :80 )'`

![alt text](<sudo ss -lptn.png>)

### Notes

Answer the following in your own words:

**1. What happens if Nginx fails to restart in production?**

If Nginx fails to restart in production, the web application will become unavailable to users.Any user visiting the site would get a connection error or timeout, since nothing would be listening on that port anymore

---

**2. What's your basic rollback plan?**
If the deployment causes issues, I would first restore the previous version of the application from a backup or redeploy the last known working build to the Nginx web root. Next, I would restore the previous Nginx configuration if it was modified, verify it with sudo nginx -t, and reload or restart Nginx. Finally, I would test the application to confirm it is accessible and functioning correctly before investigating the cause of the failed deployment. This approach minimizes downtime and quickly returns the service to a stable state.

# Task 3 — Logs & Request Trace

## Goal

Verify real traffic flow and analyze logs to understand system behavior and errors.

### Evidence

#### Screenshot 1 — Output of `sudo tail -n 30 /var/log/nginx/access.log`

![alt text](<sudo tail -n 30 access lg.png>)


#### Screenshot 2 — Output of `sudo tail -n 30 /var/log/nginx/error.log`

![alt text](<sudo tail -n 30 error log.png>)

#### Screenshot 3 — Output of `sudo journalctl -u nginx --no-pager -n 50`

![alt text](<sudo journalctl -u nginx --no-pager .png>)

### Notes

Answer the following in your own words:

**1. Were there any errors in the logs?**

- If yes, mention 1–2 example error lines from the logs and explain what each one means in simple terms.
- If no, explain what it means if the error log is empty or shows no recent errors during your check.

There is no error in the logs. 

**2. If there were no errors, what does that indicate about the system?**

No errors in the Nginx error log indicate that Nginx is running without any logged errors or warnings. While this is a positive sign, it does not by itself prove the entire application is working correctly, so other logs and functional tests should also be checked.

**3. Based on the access logs, were your curl requests visible in the log entries? What does that prove about traffic flow?**

 Yes. The curl request appeared in access.log as a GET / request from the server's own public IP with a 200 status and the user agent curl/8.18.0. This confirms the full traffic path is working end-to-end


# Task 4 — System Resource Health Check (Capacity Red Flags)

## Goal

Assess server capacity and detect potential performance or failure risks.

### Evidence

#### Screenshot 1 — Output of `uptime`

![alt text](<Uptime output.png>)

#### Screenshot 2 — Output of `free -h`

![alt text](<free -h .png>)

#### Screenshot 3 — Output of `df -h`

![alt text](<df -h.png>)

#### Screenshot 4 — Output of `sudo du -sh /var/* | sort -h`

![alt text](<sudo du -sh.png>)

### Notes

Answer the following in your own words:

**1. Which resource looks most critical right now? (CPU/load, memory, or disk) Explain why.**

None of the monitored resources are currently critical. CPU utilization is extremely low, memory usage is only 39%, and the root filesystem is approximately 60% full, leaving around 2.7 GB of free space. 


**2. What happens if disk becomes 100% full in a production server?**

If a production server's disk reaches 100% capacity, it can cause serious service disruptions. Applications may be unable to write logs, create temporary files, or save user data, leading to errors or crashes. Nginx may fail to write access and error logs, databases may stop accepting writes or become unstable, and system services may fail to start or restart. In severe cases, the server may become unresponsive or fail to boot properly after a reboot. To prevent this, disk usage should be monitored proactively, log rotation should be configured, and sufficient free disk space should always be maintained

# Task 5 — Configuration & Deployment Verification

## Goal

Ensure the correct React build is deployed and Nginx is serving it properly.

### Evidence

#### Screenshot 1 — Output of `ls -lah /var/www/html | head -n 20`

![alt text](<ls -la var www html.png>)

#### Screenshot 2 — Output of `grep -R "Deployed by" -n /var/www/html 2>/dev/null | head`

![alt text](<grep -R Deployed by.png>)


#### Screenshot 3 — Output of `grep -n "try_files" /etc/nginx/sites-available/default`

![alt text](<grep -n try files.png>)

### Notes

Answer the following in your own words:

**1. How do you confirm that the correct version of the application is deployed?**

I confirmed that the correct version of the application was deployed by verifying both the Nginx configuration and the deployed application files. The command grep -n "try_files" /etc/nginx/sites-available/default showed that Nginx is configured with try_files $uri /index.html;, which is the correct configuration for serving a React single-page application (SPA). I also listed the contents of /var/www/html and confirmed that the expected React build artifacts—such as index.html, asset-manifest.json, manifest.json, the static/ directory, and other assets—were present. Their timestamps (Jul 14 16:37) indicate they were deployed together as the latest build. These checks confirm that the intended React application build was successfully deployed and is being served by Nginx.

# Task 6 — Nginx Configuration Failure Simulation

## Goal

Simulate a real-world Nginx misconfiguration and recover the service safely.

### Evidence

#### Screenshot 1 — Output of `sudo nginx -t` showing the syntax error (broken config)

![alt text](<sudo nginx -t showing the syntax error broken config.png>)

#### Screenshot 2 — Output of `sudo nginx -t` showing syntax ok (fixed config)

![alt text](<sudo nginx -t showing the syntax error fixed .png>)

#### Screenshot 3 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

![alt text](<curl -I http public ip.png>)

### Notes

Answer the following in your own words:

**1. What caused the configuration failure?**
Missing semi colon at the end of (try_files $uri /index.html;) in the  /etc/nginx/sites-available/default caused the configuration  error


**2. How did you fix the issue?**

 I reopened the config file and restored the missing semicolon, then re-ran sudo nginx -t to confirm the syntax was valid before restarting the service.

**3. How can you avoid this kind of issue in real production systems?**

To avoid this type of issue in a production environment, configuration changes should be validated before they are applied. After editing an Nginx configuration file, I would always run sudo nginx -t to check for syntax errors, such as a missing semicolon or unmatched braces. I would only reload Nginx (sudo systemctl reload nginx) after the configuration test succeeds. 
In addition, configuration files should be version-controlled with Git,so a bad change can be instantly reverted to a known-good state instead of manually retyped from memory

Using Infrastructure as Code and automated deployment tools also helps reduce manual editing errors and ensures configuration changes are consistent and reproducible.


# Task 7 — Web Application Failure Simulation

## Goal

Simulate missing deployment content and recover the application safely.

### Evidence

#### Screenshot 1 — Output of `curl -I http://<public-ip>` showing failure (non-200 response)

![alt text](<curl -I http public ip showing failure non-200 response.png>)


#### Screenshot 2 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

![alt text](<curl -I http public ip showing ok with 200 response-1.png>)

### Notes

Answer the following in your own words:

**1. What caused the application to break in this scenario?**

The application broke because the existing web root (/var/www/html) was renamed to /var/www/html_backup, and a new, empty /var/www/html directory was created in its place. As a result, Nginx could no longer find the application's index.html and other static assets required to serve the React application. When a request was made with curl, Nginx returned an HTTP 500 Internal Server Error, indicating it was unable to serve the application due to the missing web content. 

**2. How did you fix the issue and restore the application?**

After restoring the original application files to /var/www/html the issue was resolved
Initially original deployment had been safely backed up beforehand (moved to html_backup rather than deleted), so recovery involved removing the empty broken directory and moving the backup back into place at the correct path. Nginx was restarted to ensure it was serving cleanly from the restored files, and recovery was confirmed externally via curl -I, which returned 200 OK

**3. What steps would you take to prevent this kind of issue in real production systems?**

Avoid modifying the live web root directly.
I will use staged deployments or release directories.
I will maintain backups and a rollback plan.
I will automate deployments with CI/CD.
I will run post-deployment health checks and monitor the application

# Task 8 — Security & Reliability Review

## Goal

Review and reflect on the security and reliability practices applied during this assignment.

### Security & Reliability Notes

Answer the following in your own words:

**1. Why is SSH key-based authentication more secure than sharing passwords?**

SSH key-based authentication is more secure than password authentication because it uses a cryptographic key pair instead of a shared secret. The private key remains securely on the client's device and is never transmitted over the network, while the server verifies the corresponding public key. This makes SSH keys highly resistant to brute-force attacks, password guessing, and credential theft. In contrast, passwords can be weak, reused, or exposed through phishing or leaks. 

Additionally, SSH keys are longer and more complex than typical passwords, making them significantly harder to compromise. For even greater security, private keys can be protected with a passphrase, adding an extra layer of authentication.

**2. Why should only required ports be open on a production server?**

Only the required ports should be open on a production server to minimize the attack surface and improve security. Every open port represents a potential entry point that attackers can scan and attempt to exploit. By exposing only the services that are necessary—for example, port 80/443 for web traffic and port 22 for SSH administration (ideally restricted to trusted IP addresses)—the risk of unauthorized access and attacks is significantly reduced

**3. Why is it important for Nginx to be enabled on boot?**

It is important for Nginx to be enabled on boot so that the web server starts automatically whenever the server is restarted. This ensures that the application becomes available without requiring manual intervention after planned maintenance, system updates, or unexpected reboots. If Nginx is not enabled on boot, the server may come online but the website or application will remain unavailable until Nginx is started manually, resulting in unnecessary downtime. Enabling Nginx at startup improves service availability, reliability, and helps maintain business continuity in a production environment.

**4. What are the risks of sharing secrets, keys, or credentials publicly?**

Sharing secrets, keys, or credentials publicly can lead to unauthorized access, data breaches, and system compromise. If attackers obtain SSH private keys, API keys, passwords, or cloud access credentials, they may be able to log in to servers, access sensitive data, modify or delete resources, deploy malicious code, or incur unexpected cloud costs. Exposed credentials can also result in service outages, financial losses, and damage to an organization's reputation

**5. Why should cloud resources be stopped or terminated when they are no longer needed?**

Cloud resources should be stopped or terminated when they are no longer needed to reduce costs, improve security, and optimize resource utilization. Running resources such as EC2 instances, databases, and load balancers continue to incur charges even when they are idle. Unused resources can also increase the attack surface if they remain accessible and unpatched.

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/dHukeGgv

#### Screenshot — Published LinkedIn post

![alt text](<Production drill linked post.png>)

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [ ] Task 1: Screenshots (browser, ip a, ss -tulpen, ufw status) + Notes answered
- [ ] Task 2: Screenshots (nginx status, nginx -t, ss port 80) + Notes answered
- [ ] Task 3: Screenshots (access log, error log, journalctl) + Notes answered
- [ ] Task 4: Screenshots (uptime, free -h, df -h, du -sh) + Notes answered
- [ ] Task 5: Screenshots (ls html, grep deployed by, grep try_files) + Notes answered
- [ ] Task 6: Screenshots (nginx -t fail, nginx -t pass, curl recovery) + Notes answered
- [ ] Task 7: Screenshots (curl failure, curl recovery) + Notes answered
- [ ] Task 8: Security & Reliability Notes answered
- [ ] LinkedIn post published and URL submitted
- [ ] Full Name visible in all required screenshots
- [ ] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*