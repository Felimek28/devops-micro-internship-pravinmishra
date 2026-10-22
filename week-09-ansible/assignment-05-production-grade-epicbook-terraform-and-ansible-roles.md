# Assignment — Deploy EpicBook with Terraform and Ansible Roles

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will deploy the EpicBook web application using Terraform and Ansible roles.

Terraform provisions the cloud infrastructure, including one Ubuntu VM and one managed MySQL database. Ansible roles configure the VM, install required software, deploy the EpicBook application, configure Nginx, connect the app to the managed MySQL database, and verify the deployment.

---

# Task 1 — Set Up the Project Folder Layout

## Goal

Create the project folder structure for Terraform and Ansible roles.

Terraform will be used to provision the cloud infrastructure. Ansible roles will be used to configure the VM and deploy the EpicBook application.

### Evidence

#### Screenshot 1 — Terminal showing the completed `epicbook-prod` project structure

![alt text](<Terminal showing the completed epicbook-prod project structure.png>)

### Notes

Answer the following in your own words:

**1. Which cloud provider did you choose for this assignment?**

  AWS

**2. Why is it useful to keep Terraform files and Ansible files in separate folders?**

Keeping Terraform and Ansible files in separate folders helps maintain a clear and organized project structure. Terraform is responsible for provisioning and managing infrastructure, such as AWS EC2 instances, networks, and security groups, while Ansible is used to configure the servers, install software, and manage applications. Separating the files makes the project easier to maintain, troubleshoot, and reuse.

**3. What is the purpose of the `roles` directory in Ansible?**

The roles directory stores reusable and organized units of Ansible automation. Each role can contain tasks, handlers, variables, templates, and files for a specific configuration purpose, such as installing Nginx or configuring a web server. Roles make playbooks easier to read, maintain, and reuse across multiple servers and projects, including my epicbook-prod project.

# Task 2 — Provision the Infrastructure with Terraform

## Goal

Run Terraform to provision the cloud infrastructure for the EpicBook deployment.

Terraform will create the VM, managed MySQL database, networking, security rules, and required outputs.

### Evidence

#### Screenshot 2 — `terraform apply` completed successfully

![alt text](<terraform apply completed successfully.png>)

#### Screenshot 3 — Output of `terraform output`

![alt text](<Output of terraform output.png>)

#### Screenshot 4 — Azure Portal or AWS Console showing the VM running

![alt text](<AWS Console showing the VM running.png>)


#### Screenshot 5 — Azure Portal or AWS Console showing the managed MySQL database created

![alt text](<AWS Console showing the managed MySQL database created.png>)

### Notes

Answer the following in your own words:

**1. What resources did Terraform create for this assignment?**

Terraform created 12 AWS resources for my EpicBook project. These included a Virtual Private Cloud (VPC), a public subnet, two private database subnets, an Internet Gateway, a public route table and its association, EC2 and RDS security groups, an RDS database subnet group, an EC2 instance, and a MySQL RDS database. These resources provide the network infrastructure, web server, and private database environment required for the application.


**2. Why should you review `terraform plan` before running `terraform apply`?**

Reviewing terraform plan helps me understand what Terraform intends to create, modify, or destroy before making changes to my AWS environment. It allows me to identify configuration errors, unexpected changes, and potential security or cost issues. This helps me avoid accidental resource deletion, unnecessary expenses, and unwanted infrastructure changes.


**3. Why should database passwords not be shown in Terraform output?**

Database passwords should not be shown in Terraform output because they are sensitive credentials that could be exposed to unauthorized people. If someone gains access to these passwords, they could access or manipulate the database and compromise application data. I should protect database credentials by using secure methods such as AWS Secrets Manager or environment variables, avoid committing them to Git, and use Terraform's sensitive-value features where appropriate.


# Task 3 — Verify SSH Key-Based Access

## Goal

Verify that the cloud VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 6 — Successful SSH hostname check from the Ansible controller

![alt text](<Successful SSH hostname check from the Ansible controller.png>)


### Notes

Answer the following in your own words:

**1. What command did you use to verify SSH access?**

I used the following command to connect to my AWS EC2 instance using my private SSH key and retrieve the instance's hostname.
ssh -i ~/.ssh/react-key.pem ubuntu@44.199.201.81 "hostname"


**2. What proves that SSH key-based access worked successfully?**

The command executed successfully and returned ip-10-0-1-24, which is the hostname of my EC2 instance. This confirms that I successfully authenticated using my private SSH key and executed a command on the remote server without entering a server password.


**3. What would you check if SSH returned `Permission denied (publickey)`?**

I would verify that I am using the correct private key (~/.ssh/react-key.pem) that matches the public key configured on my EC2 instance. I would also confirm that the SSH username is ubuntu, check the private key's file permissions, and ensure that the EC2 instance was launched with the correct key pair. If necessary, I would review the instance configuration and SSH access settings to identify the problem.

# Task 4 — Create the Ansible Inventory and Configuration

## Goal

Create the Ansible inventory file and local Ansible configuration for the EpicBook VM.

The inventory tells Ansible which VM to manage and which SSH user to use.

### Evidence

#### Screenshot 7 — `inventory.ini` showing the VM under the `web` group

![alt text](<inventory.ini showing the VM under the web group.png>)


#### Screenshot 8 — Output of `ansible-inventory -i inventory.ini --graph`

![alt text](<Output of ansible-inventory -i inventory.ini -graph for epicbook.png>)


#### Screenshot 9 — Output of `ansible web -i inventory.ini -m ping`

![alt text](<Output of ansible web -i inventory.ini -m ping.png>)


### Notes

Answer the following in your own words:

**1. What is the purpose of `inventory.ini`?**

The purpose of inventory.ini is to tell Ansible which servers it should manage. It contains information about the target machines, how they are grouped, and the connection details Ansible needs to reach them. In my EpicBook project, it identifies my AWS EC2 instance under the [web] group

**2. What does `ansible_host` store?**

ansible_host stores the IP address or hostname Ansible uses to connect to a target server. In my project, it contains the public IP address of my EpicBook EC2 instance, allowing Ansible to locate the server over the network


**3. What does `ansible_ssh_private_key_file` tell Ansible?**

ansible_ssh_private_key_file tells Ansible the location of the private SSH key it should use to authenticate when connecting to the target server. In my project, it points to react-key.pem, which matches the key configured for my EC2 instance


**4. Why is `host_key_checking = False` used only for this temporary lab?**

host_key_checking = False disables SSH host-key verification, making it easier to connect to temporary lab servers without manually verifying their host keys. However, it reduces security because Ansible will not verify the server's identity through this check. For production environments, I would keep host-key checking enabled to help protect against man-in-the-middle attacks



# Task 5 — Create the Main Ansible Playbook

## Goal

Create the main Ansible playbook that runs the required roles in the correct order.

The `site.yml` file will call the `common`, `nginx`, and `epicbook` roles.

### Evidence

#### Screenshot 10 — `site.yml` showing the roles in the correct order

![alt text](<site.yml showing the roles in the correct order.png>)


#### Screenshot 11 — Output of `ansible-playbook -i inventory.ini site.yml --syntax-check`

![alt text](<Output of ansible-playbook -i inventory.ini site.yml -syntax-check.png>)


### Notes

Answer the following in your own words:

**1. What is the purpose of `site.yml`?**

The purpose of site.yml is to serve as the main Ansible playbook that controls the configuration of my EpicBook server. It specifies which servers Ansible should manage and the roles to execute on them. This helps me automate server setup and application deployment in an organized and repeatable way

**2. Why should the roles run in the order `common`, `nginx`, and `epicbook`?**

The roles should run in this order because each role prepares the server for the next stage. The common role installs basic packages and performs general system configuration. The nginx role installs and configures the web server. Finally, the epicbook role deploys and configures the EpicBook application. Following this order helps ensure that the required software and services are ready before the application is deployed.


**3. What does `become: true` allow Ansible to do?**

become: true allows Ansible to execute tasks with elevated privileges, usually as the root user through sudo. This is necessary for administrative tasks such as installing packages, modifying system configuration files, and managing services like Nginx


# Task 6 — Create the `common` Role

## Goal

Create the `common` role to prepare the Ubuntu VM with the basic packages required for the EpicBook deployment.

This role handles the common server setup before Nginx and the application are configured.

### Evidence

#### Screenshot 12 — `roles/common/tasks/main.yml` showing the common setup tasks

![alt text](<roles slash common slash tasks slash main.yml showing the common setup tasks.png>)


### Notes

Answer the following in your own words:

**1. What is the responsibility of the `common` role?**

The common role is responsible for preparing my EpicBook EC2 server with basic system requirements. It can update package information, install commonly needed utilities such as Git and curl, and perform general system configuration. This ensures the server is ready for the application-specific setup


**2. Why should Nginx installation not be placed inside the `common` role?**

Nginx installation should be handled by a separate nginx role because each Ansible role should have a clear and specific responsibility. Keeping Nginx separate makes the configuration easier to maintain, troubleshoot, reuse, and update without affecting the general server setup.

**3. Why is `mysql-client` useful in this deployment?**

mysql-client provides command-line tools that allow me to connect to and interact with my MySQL RDS database from the EC2 instance. It is useful for testing database connectivity, running SQL queries, and troubleshooting database connection issues without installing a full MySQL database server on the EC2 instance.


# Task 7 — Create the `nginx` Role

## Goal

Create the `nginx` role to install Nginx and configure it as a reverse proxy for the EpicBook application.

Nginx will receive browser traffic on port `80` and forward it to the EpicBook Node.js application running on the VM.

### Evidence

#### Screenshot 13 — `roles/nginx/tasks/main.yml` showing Nginx installation and site configuration tasks

![alt text](<roles slash nginx slash tasks slash main.yml showing Nginx installation and site configuration tasks.png>)


#### Screenshot 14 — `roles/nginx/templates/epicbook.conf.j2` showing the reverse proxy configuration

![alt text](<roles slash nginx slash templates slash epicbook.conf.j2 showing the reverse proxy configuration.png>)


### Notes

Answer the following in your own words:

**1. What is the responsibility of the `nginx` role?**

The nginx role is responsible for installing, configuring, and managing the Nginx web server on my EpicBook EC2 instance. It ensures that Nginx is running and configured to receive incoming HTTP requests and forward them to the EpicBook application.


**2. Why is Nginx configured as a reverse proxy in this deployment?**

Nginx is configured as a reverse proxy to receive requests from users and forward them to the EpicBook application running on a separate application port. This allows users to access the application through the web server without connecting directly to the application's internal port. It also provides a central point for handling HTTP requests and can improve security and request management.


**3. Why should the application port come from `group_vars/web.yml` instead of being hard-coded?**

The application port should come from group_vars/web.yml so that it can be managed in one place rather than repeated throughout the configuration files. This makes the Ansible setup easier to maintain and reuse. If the application port changes, I can update the variable in one file without having to edit multiple templates or tasks.

# Task 8 — Create the `epicbook` Role

## Goal

Create the `epicbook` role to deploy the EpicBook application, connect it to the managed MySQL database, and run the application on port `8080` using PM2.

### Evidence

#### Screenshot 15 — `roles/epicbook/tasks/main.yml` showing application deployment tasks

![alt text](<roles slash epicbook slash tasks slash main.yml showing application deployment tasks.png>)

#### Screenshot 16 — Task or file showing how the database connection is configured, with secrets hidden

![alt text](<Task or file showing how the database connection is configured, with secrets hidden-1.png>)


#### Screenshot 17 — Task or output showing the EpicBook application managed by PM2

![alt text](<Task or output showing the EpicBook application managed by PM2.png>)

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `epicbook` role?**

The epicbook role is responsible for deploying and configuring the EpicBook Node.js application on the EC2 instance. It handles the application setup, environment variables, database connection settings, and starting the application with PM2.

**2. Why is PM2 used for the EpicBook Node.js application?**

PM2 is used to manage the Node.js application process and keep it running in the background. It can automatically restart the application if it crashes, monitor its status, and help configure it to start again after a server reboot.


**3. Why should database passwords not be hard-coded in public files?**

Database passwords should not be hard-coded in public files because anyone who accesses those files could obtain the credentials and gain unauthorized access to the database. Passwords should be stored securely using tools such as Ansible Vault and kept out of public GitHub repositories


**4. What does it mean for the application to run on port `8080` while Nginx listens on port `80`?**

It means Nginx receives incoming HTTP requests on port 80 and forwards them to the EpicBook Node.js application running on port 8080. Users can access the application through the web server without connecting directly to the application's port. This also allows the application port to remain private while Nginx handles incoming web traffic

# Task 9 — Create Group Variables

## Goal

Create reusable variables for the EpicBook deployment.

The `group_vars/web.yml` file stores values that can be reused across the Ansible roles.

### Evidence

#### Screenshot 18 — `group_vars/web.yml` showing the application, PM2, and database variables, with passwords hidden or masked

![alt text](<group_vars slash web.yml showing the application, PM2, and database variables, with passwords hidden or masked.png>)


### Notes

Answer the following in your own words:

**1. What is the purpose of `group_vars/web.yml`?**

The purpose of group_vars/web.yml is to store variables that are shared by all servers in the Ansible web group. It helps keep configuration values separate from playbooks, making the project easier to maintain, reuse, and manage. In my EpicBook production project, I used group variables to configure the web application deployment.

**2. Which values did you store in `group_vars/web.yml`?**

I stored application configuration values such as the application repository URL, the destination directory on the server, the application user, and other settings required to deploy and run the EpicBook application. In my project, these variables are organized into group_vars/web/vars.yml and group_vars/web/vault.yml rather than a single group_vars/web.yml file.


**3. How did you handle the database password securely?**

I handled the database password securely using Ansible Vault. I stored sensitive information in the encrypted group_vars/web/vault.yml file instead of writing the password in plain text in my playbooks or regular variables files. I used the Vault password when running Ansible commands to decrypt the secrets when needed. This approach helps protect sensitive credentials from being exposed in my project repository


# Task 10 — Run the Ansible Playbook

## Goal

Run the Ansible playbook to configure the VM and deploy the EpicBook application.

The playbook should run the roles in this order:

1. `common`
2. `nginx`
3. `epicbook`

### Evidence

#### Screenshot 19 — Ansible playbook output showing the roles running

![alt text](<Ansible playbook output showing the roles running.png>)


#### Screenshot 20 — Final Ansible recap showing `failed=0`

![alt text](<Final Ansible recap showing failed=0.png>)

#### Screenshot 21 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx" --become`

![alt text](<Output of ansible web -i inventory.ini -m command -a systemctl is-active nginx -become.png>)


#### Screenshot 22 — Output of `ansible web -i inventory.ini -m command -a "pm2 status"`

![alt text](<Output of ansible web -i inventory.ini -m command -a pm2 status.png>)


#### Screenshot 23 — Output of `ansible web -i inventory.ini -m command -a "curl -I http://localhost:8080"`

![alt text](<Output of ansible web -i inventory.ini -m command -a curl -I http localhost 8080.png>)

### Notes

Answer the following in your own words:

**1. What command did you run to execute the Ansible playbook?**

I ran the following command to execute the Ansible playbook and deploy the EpicBook application:
ansible-playbook -i inventory.ini site.yml --ask-vault-pass

The command uses inventory.ini to identify the target server, executes the tasks defined in site.yml, and prompts for the Ansible Vault password needed to decrypt protected variables.

**2. How do you know all roles completed successfully?**

I checked the playbook execution output and the final PLAY RECAP. It showed failed=0 and unreachable=0, confirming that the playbook completed without task failures or connection errors. The output also showed that the tasks for the Common, Nginx, and EpicBook roles executed successfully.

**3. What proves that Nginx is active?**

I verified the Nginx service using the systemctl command. It returned active, confirming that Nginx was running on the EC2 instance. I also accessed the public application URL and confirmed that Nginx was serving the EpicBook webpage.

**4. What proves that PM2 is managing the EpicBook application?**

I ran pm2 status on the EC2 instance and confirmed that EpicBook appeared in the process list with its status shown as online. This demonstrated that PM2 was managing the Node.js application process.


**5. What proves that the EpicBook application responds on port `8080`?**

I tested the application locally on the EC2 instance using curl against port 8080. The command returned HTTP status 200, and the response contained the EpicBook webpage HTML, including the title The EpicBook! This confirmed that the Node.js application was responding successfully on port 8080.


# Task 11 — Verify the EpicBook Deployment

## Goal

Verify that the EpicBook application is running, accessible in the browser, and connected to the managed MySQL database.

### Evidence

#### Screenshot 24 — Output of `curl -I http://<public_ip>`

![alt text](<Curl -I for epicbook-prod public ip.png>)

#### Screenshot 25 — Output of the cart API test command

![alt text](<Output of the cart API test command.png>)


#### Screenshot 26 — Output of the `/cart` HTTP status check

![alt text](<Output of the slash cart  HTTP status check.png>)


#### Screenshot 27 — Browser showing the EpicBook application loaded from `http://<public_ip>`

![alt text](<Browser showing the EpicBook application loaded from http public_ip.png>)

### Notes

Answer the following in your own words:

**1. What HTTP response did you receive from the public application URL?**

I received an HTTP 200 OK response from the public application URL, http://44.199.201.81. This confirmed that the application was accessible over HTTP and that the EpicBook webpage was being served correctly through Nginx.

**2. What did the cart API test prove?**

The cart API test returned HTTP 200 OK with the JSON response {"cart":[],"book":null}. This proved that the cart API endpoint was reachable and responding successfully. The response indicated that the cart was empty and no book was selected in the response.


**3. What did the `/cart` status check return?**

I used a curl command to check the HTTP status of the /cart route. The command returned the HTTP status code displayed in my terminal, which helped me verify whether the cart page was accessible.

Before submitting, replace this sentence with your actual status code from running the /cart check.


**4. What issue did you face during verification, and how did you fix it?**

During verification, the public application URL initially displayed the default Nginx welcome page instead of the EpicBook application. I checked the Nginx site configuration, confirmed that the EpicBook site was enabled, and verified that the Node.js application was running on port 8080 under PM2. I then reloaded Nginx and tested the public URL again. The application subsequently displayed the correct EpicBook webpage, confirming that the reverse proxy was working as expected.


# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/felix-nwobodo-2a191856_devops-aws-terraform-ugcPost-7518376592578965505-Rble/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE

#### Screenshot — Published LinkedIn post

![alt text](<Linkedin post for epicbook-prod.png>)


# Assignment Questions

Answer the following in your own words:

**1. Why is Terraform used for infrastructure provisioning?**

Terraform is used to create and manage cloud infrastructure automatically. In my EpicBook project, I used Terraform to provision AWS resources such as the EC2 instance, networking components, security groups, and managed MySQL database. This made the infrastructure repeatable, consistent, and easier to maintain.


**2. Why are Ansible roles useful for production-style deployments?**

Ansible roles help organize configuration and deployment tasks into reusable, manageable components. In my project, I separated the tasks into common, nginx, and epicbook roles. This made the playbook easier to understand, troubleshoot, update, and reuse across different environments.

**3. What is the purpose of `group_vars/web.yml`?**

The purpose of group_vars/web.yml is to define variables shared by hosts in the web inventory group. These variables include application settings, the application port, database connection details, and Nginx configuration values. This avoids repeating the same settings in multiple playbooks and makes configuration easier to manage.

**4. Why should database passwords not be committed to GitHub?**

Database passwords should not be committed to GitHub because unauthorized people could access them and use them to connect to the database. This could lead to data theft, unauthorized changes, or additional cloud costs. In my project, I used Ansible Vault to protect sensitive variables, keeping the database password encrypted instead of exposing it in plain text

**5. What is the purpose of Nginx in this deployment?**

Nginx acts as a reverse proxy in my EpicBook deployment. It receives HTTP requests on port 80 and forwards them to the Node.js application running on port 8080. This allows users to access the application through the public IP address without connecting directly to the Node.js port


**6. Why should the managed MySQL database not be publicly accessible?**

The managed MySQL database should not be publicly accessible because exposing it to the internet increases the risk of unauthorized access, brute-force attacks, and data breaches. Keeping it in private subnets and restricting access through security groups allows only authorized application servers to connect to the database.


**7. Why is PM2 used for the EpicBook Node.js application?**

PM2 is used to manage the EpicBook Node.js application process. It keeps track of the application, helps restart it if it crashes, provides process monitoring and logs, and supports process management commands. In my deployment, I verified that EpicBook appeared as online in the PM2 process list.

**8. What does idempotency mean in Ansible?**

Idempotency means that running an Ansible playbook repeatedly should keep the system in the desired state without making unnecessary changes. For example, if Nginx is already installed and configured correctly, running the playbook again should not reinstall or reconfigure it unnecessarily. This makes deployments more predictable and reliable.

**9. What issue did you face during the deployment, and how did you fix it?**

During verification, the public application URL initially displayed the default Nginx welcome page instead of the EpicBook application. I checked the Nginx site configuration, verified that the EpicBook site was enabled, and confirmed that the Node.js application was running under PM2 on port 8080. I then reloaded Nginx and tested the public URL again. The correct EpicBook webpage appeared, and the cart API returned HTTP 200 OK with a JSON response.


**10. What security improvement would you make before using this setup in production?**

Before using this setup in production, I would strengthen security by enabling HTTPS with a valid TLS certificate, restricting SSH access to trusted IP addresses, and keeping the MySQL database in private subnets. I would also use AWS Secrets Manager or another secure secret-management solution, apply least-privilege IAM permissions, and configure monitoring, logging, backups, and regular security updates. These improvements would help protect the application, its infrastructure, and its users' data.
-

# Required Files

Confirm that the following files are included in your GitHub repository or assignment folder:

- [ ] `README.md`
- [ ] Terraform files under either `terraform/azure/` or `terraform/aws/`
- [ ] `ansible/ansible.cfg`
- [ ] `ansible/inventory.ini`
- [ ] `ansible/site.yml`
- [ ] `ansible/group_vars/web.yml`
- [ ] `ansible/roles/common/tasks/main.yml`
- [ ] `ansible/roles/nginx/tasks/main.yml`
- [ ] `ansible/roles/nginx/templates/epicbook.conf.j2`
- [ ] `ansible/roles/epicbook/tasks/main.yml`

---

# Submission Instructions

- Add all required screenshots in your submission.
- Full Name must be visible in required screenshots.
- Mention the cloud provider used: Azure or AWS.
- Add the VM public IP address.
- Add the final application URL.
- Add Terraform output proof.
- Add Ansible role tree proof.
- Add all required notes and assignment question answers.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, cloud credentials, database credentials, Terraform state files, subscription IDs, or account IDs.

---

# Completion Checklist

- [ ] Task 1: Project folder layout created
- [ ] Task 2: Terraform infrastructure provisioned
- [ ] Task 3: SSH key-based access verified
- [ ] Task 4: Ansible inventory and configuration created
- [ ] Task 5: Main Ansible playbook created
- [ ] Task 6: `common` role created
- [ ] Task 7: `nginx` role created
- [ ] Task 8: `epicbook` role created
- [ ] Task 9: Group variables created
- [ ] Task 10: Ansible playbook run completed
- [ ] Task 11: EpicBook deployment verified
- [ ] Terraform files created under only one cloud provider folder
- [ ] One Ubuntu VM was created
- [ ] One managed MySQL database was created
- [ ] SSH port `22` is restricted to the controller public IP
- [ ] HTTP port `80` is accessible
- [ ] MySQL port `3306` is not publicly open
- [ ] `ansible web -i inventory.ini -m ping` returns `SUCCESS`
- [ ] `site.yml` calls the roles in the correct order
- [ ] Database secrets are hidden or handled securely
- [ ] Nginx is active
- [ ] PM2 shows the EpicBook application running
- [ ] EpicBook responds on port `8080`
- [ ] Public URL loads in the browser
- [ ] Cart API verification works
- [ ] Playbook completes with `failed=0`
- [ ] Screenshots 1–27 are included
- [ ] Assignment questions are answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information is exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra and The CloudAdvisory, focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## Resources

- DMI Official Website: [https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme)
- University: [https://university.pravinmishra.com?utm_source=github&utm_medium=readme](https://university.pravinmishra.com?utm_source=github&utm_medium=readme)
- Discord Community: [https://discord.pravinmishra.com?utm_source=github&utm_medium=readme](https://discord.pravinmishra.com?utm_source=github&utm_medium=readme)
- Blog: [https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*