# Assignment 02 — Provision Linux VMs with Terraform and Run Ansible Ad-Hoc Commands

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will use Terraform to provision three or four Ubuntu Linux Virtual Machines on either Microsoft Azure or Amazon Web Services.

You will configure SSH key-based authentication, organize the servers using a custom Ansible inventory, and run Ansible ad-hoc commands across individual hosts and inventory groups.

---

# Task 1 — Create the Multi-Host Lab Structure

## Goal

Create a separate project directory for the multi-host lab and prepare the Terraform, Ansible, and documentation files.

This project will use the Git repository and Ansible controller prepared in Assignment 01.

### Evidence

#### Screenshot 1 — Terminal showing the complete `ansible-adhoc-lab` project structure

![alt text](<Terminal showing the complete ansible-adhoc-lab project structure.png>)


#### Screenshot 2 — Terminal showing `git status --short` with the new project files and updated `.gitignore`


![alt text](<Terminal showing git status --short.png>)


### Notes

1.Organized Project Structure: The project includes dedicated directories for Terraform, Ansible, and documentation, ensuring a clear separation between infrastructure provisioning, configuration management, and project documentation.

2.Reuse of Existing Environment: The existing Git repository and Ansible controller from Assignment 01 were reused, allowing the lab to build on the previously configured environment without setting up a new Ansible controller

3.Improved Version Control and Security: The .gitignore file was updated to exclude generated files, Terraform state files, local environment configurations, and other unnecessary or sensitive files from version control.

4.Verification Before Committing: The command git status --short was used to review the new project files and .gitignore changes, ensuring that the intended changes were identified before committing them to the repository.

# Task 2 — Create the Terraform Configuration

## Goal

Create the Terraform configuration required to provision three or four Ubuntu Linux VMs on your selected cloud platform.

Complete only one option:

- Option A — Microsoft Azure
- Option B — Amazon Web Services

Do not configure both providers for this assignment.

### Evidence

#### Screenshot 3 — Terraform configuration showing the three or four server roles and the `for_each` or `count` implementation

![alt text](<Terraform configuration showing the three or four server roles 1.png>)


#### Screenshot 4 — Terraform configuration showing SSH restricted to the controller IP and HTTP allowed only for web hosts

![alt text](<Terraform configuration showing SSH restricted to the controller IP and HTTP allowed only for web hosts.png>)



#### Screenshot 5 — Terraform output configuration showing how public IP addresses are associated with the server roles

![alt text](<Terraform output configuration showing how public IP addresses are associated with the server roles.png>)


### Notes

I used Amazon Web Services (AWS) and selected the three-VM option for this assignment. I defined three server roles—web1, app1, and db1—and used Terraform's for_each meta-argument to provision the EC2 instances consistently from a single configuration. I configured an Ubuntu AMI, VPC, public subnet, Internet Gateway, route table, security groups, and an AWS key pair using my existing SSH public key.

For security, I restricted SSH access on port 22 to my Ansible controller's current public IP address using a /32 CIDR block, rather than allowing access from anywhere. I also configured Terraform outputs to map each server role to its corresponding public IP address, making it easier to identify and manage the instances during subsequent Ansible configuration and automation tasks.


# Task 3 — Provision the Infrastructure with Terraform

## Goal

Initialize and validate the Terraform configuration, review the execution plan, provision the selected three or four VMs, and retrieve their public IP addresses.

### Evidence

#### Screenshot 6 — Final `terraform apply` output showing `Apply complete`

![alt text](<terraform apply completed.png>)



#### Screenshot 7 — `terraform output public_ips` showing the role-to-IP mapping for all three or four VMs

![alt text](<terraform apply  for assignment 2.png>)



#### Screenshot 8 — Azure Portal or AWS Management Console showing all three or four VMs in the `Running` state, with their role-based names visible

![alt text](<AWS Management Console showing all three or four VMs in the Running state.png>)


### Notes

I initialized the Terraform working directory, formatted and validated the configuration, reviewed the execution plan, and applied the configuration to AWS. The deployment completed successfully, provisioning 11 resources, including three Ubuntu EC2 instances: web1, app1, and db1. I used terraform output public_ips to retrieve the public IP addresses mapped to each instance. Finally, I verified in the AWS Management Console that all three EC2 instances were running successfully.

# Task 4 — Verify SSH Key-Based Access

## Goal

Verify that each managed VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 9 — Terminal showing successful SSH hostname output from all VMs

![alt text](<Terminal showing successful SSH hostname output from all VMs.png>)

### Notes

I successfully verified SSH key-based authentication from my WSL Ansible controller to all three Ubuntu EC2 instances: web1, app1, and db1. I retrieved their public IP addresses from the Terraform outputs and connected using the ubuntu user and my ED25519 SSH private key. I then executed the hostname command remotely on each instance and confirmed successful connectivity. During testing, I also updated the SSH security-group rule to reflect my controller’s current public IP address using a /32 CIDR, ensuring that SSH access remained restricted to my current public IP.


# Task 5 — Create the Custom Ansible Inventory

## Goal

Create an Ansible inventory file that groups the managed VMs by role.

The inventory allows Ansible to run commands against all servers, or only specific groups such as `web`, `app`, or `db`.

### Evidence

#### Screenshot 10 — `inventory.ini` showing the `web`, `app`, and `db` groups

![alt text](<inventory.ini showing the web, app, and  db groups.png>)


#### Screenshot 11 — Output of `ansible-inventory -i inventory.ini --graph`


![alt text](<Output of ansible-inventory -i inventory.ini -graph.png>)

### Notes

I created a custom inventory.ini file and organized the three managed EC2 instances into the [web], [app], and [db] groups. Since my Ansible controller runs outside the AWS VPC, I configured each host using its EC2 public IP address. I also specified the ubuntu SSH user and the path to my existing private key for secure authentication. Finally, I ran ansible-inventory -i inventory.ini --graph to validate the inventory structure and confirm that web1, app1, and db1 were assigned to the correct groups.


# Task 6 — Run Ansible Ad-Hoc Commands

## Goal

Run Ansible ad-hoc commands from the controller to verify connectivity, check server information, and manage packages and services across inventory groups.

This task proves that the inventory is working and that Ansible can control multiple managed VMs without writing a playbook.

### Evidence

#### Screenshot 12 — Output of `ansible all -i inventory.ini -m ping`

![alt text](<Output of ansible all -i inventory.ini -m ping.png>)


#### Screenshot 13 — Output of `ansible all -i inventory.ini -m command -a "uptime"`

![alt text](<Output of ansible all -i inventory.ini -m command -a uptime.png>)


#### Screenshot 14 — Output of `ansible web -i inventory.ini -m apt -a "name=nginx state=present update_cache=yes" --become`

![alt text](<ansible web -i inventory.ini -m apt -a name=nginx state=present update_cache=yes -become.png>)

#### Screenshot 15 — Output of `ansible web -i inventory.ini -m service -a "name=nginx state=started enabled=yes" --become`

![alt text](<ansible web -i inventory.ini -m service -a name=nginx state=started enabled=yes -become.png>)


#### Screenshot 16 — Output of `ansible all -i inventory.ini -m apt -a "name=htop state=present update_cache=yes" --become`


![alt text](<ansible all -i inventory.ini -m apt -a name=htop state=present update_cache=yes -become.png>)


#### Screenshot 17 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx"`

![alt text](<ansible web -i inventory.ini -m command -a systemctl is-active nginx.png>)

### Notes

I used Ansible ad-hoc commands to administer three AWS EC2 instances without creating a playbook. I began by using the Ansible `ping` module to verify SSH connectivity and Python execution on all managed hosts, then used the `command` module to check their uptime.

Next, I targeted only the `web` inventory group to install Nginx, start the service, and enable it to launch automatically at boot, using privilege escalation with `--become`. I then installed `htop` across all three managed servers and verified that Nginx was running and reported an active status on the web server.

This exercise demonstrated how Ansible ad-hoc commands can simplify routine server administration, while inventory groups enable targeted, role-specific operations or environment-wide tasks from a single control node.



# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/felix-nwobodo-2a191856_devops-aws-terraform-ugcPost-7516549868560068609-mqOk/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE

#### Screenshot — Published LinkedIn post

![alt text](<Linkedpost for ansible assg 2.png>)

# Assignment Questions

Answer the following in your own words:

**1. What is the purpose of an Ansible inventory file?**

An Ansible inventory file identifies the servers that Ansible manages. It contains information such as the hosts' IP addresses, connection details, and group membership. It allows me to organize servers into groups and run commands against specific hosts or multiple servers from one control node

**2. What is the difference between the `web`, `app`, and `db` groups in your inventory?**

The web, app, and db groups organize my EC2 instances according to their intended roles. The web group contains the web server (web1), which handles web services such as Nginx. The app group contains the application server (app1), which is intended to run application logic. The db group contains the database server (db1), which is intended to handle database services. Grouping the servers this way allows me to target each role separately when running Ansible commands.


**3. What does the Ansible `ping` module verify?**

The Ansible ping module checks whether Ansible can connect to a managed host, execute the required Python code, and receive a successful response. It is different from the regular network ping command because it tests Ansible's ability to communicate with the server, typically over SSH, rather than simply checking network reachability.


**4. Why do package installation commands require `--become`?**

Package installation commands usually require administrative privileges because they modify system directories and package databases. The --become option allows Ansible to execute the command with elevated privileges, typically through sudo, so it can install software such as Nginx and htop.

**5. When would you use an ad-hoc command instead of a playbook?**

I would use an ad-hoc command for quick, one-time administrative tasks, such as checking server uptime, testing connectivity, or installing a package on a few servers. I would use a playbook for more complex, repeatable tasks that require multiple steps, structured configuration, and consistent execution across several servers.


**6. What is one challenge you faced while setting up SSH or inventory, and how did you fix it?**

One challenge I faced was troubleshooting connectivity and configuration issues while setting up my AWS EC2 instances for Ansible. Some instances could not reach Ubuntu package repositories because their outbound security group rules allowed SSH traffic only. I investigated the problem using AWS VPC Flow Logs, identified rejected outbound traffic, and added the required HTTP and HTTPS outbound rules. I then tested connectivity with curl and received an HTTP 200 OK response from the Ubuntu repository. This taught me the importance of checking network security rules, SSH connectivity, and inventory configuration when troubleshooting Ansible-managed servers.

# Required Files

Confirm that the following files are included in your assignment workspace:

- [ ] `ansible-adhoc-lab/README.md`
- [ ] `ansible-adhoc-lab/terraform/providers.tf`
- [ ] `ansible-adhoc-lab/terraform/main.tf`
- [ ] `ansible-adhoc-lab/terraform/variables.tf`
- [ ] `ansible-adhoc-lab/terraform/outputs.tf`
- [ ] `ansible-adhoc-lab/ansible/inventory.ini`
- [ ] Updated `.gitignore`

---

# Submission Instructions

- Add all required screenshots from the tasks.
- Full Name must be visible in required screenshots.
- Mention whether you used Azure or AWS.
- Mention whether you used the three-VM option or four-VM option.
- Add the public IP addresses of the VMs, redacted if preferred.
- Add your `inventory.ini` proof.
- Add a short explanation of what you learned.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, Terraform state files, cloud credentials, passwords, access keys, secret keys, account IDs, or subscription IDs.

---

# Completion Checklist

- [ ] Task 1: `ansible-adhoc-lab` project structure created
- [ ] Task 1: `.gitignore` updated for Terraform files
- [ ] Task 2: Terraform configuration created
- [ ] Task 2: Server roles defined for either three or four VMs
- [ ] Task 2: `count` or `for_each` used
- [ ] Task 2: SSH restricted to the controller public IP
- [ ] Task 2: HTTP allowed only for web hosts
- [ ] Task 2: Terraform output maps roles to public IPs
- [ ] Task 3: Terraform initialized successfully
- [ ] Task 3: Terraform configuration validated
- [ ] Task 3: Terraform apply completed successfully
- [ ] Task 3: All selected VMs are running
- [ ] Task 4: SSH key-based access works for every VM
- [ ] Task 5: `inventory.ini` contains `web`, `app`, and `db` groups
- [ ] Task 5: `ansible-inventory -i inventory.ini --graph` shows the correct groups
- [ ] Task 6: `ansible all -i inventory.ini -m ping` returns `SUCCESS`
- [ ] Task 6: Ad-hoc commands run successfully
- [ ] Task 6: `--become` was used for package and service tasks
- [ ] Task 6: Nginx is active on the `web` group
- [ ] Screenshots 1–17 are included
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