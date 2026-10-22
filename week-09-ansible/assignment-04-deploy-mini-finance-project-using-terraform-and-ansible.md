# Assignment 04 — Deploy Mini Finance on Azure Using Terraform and Ansible

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will provision Azure infrastructure using Terraform and deploy the Mini Finance website using an Ansible multi-play playbook.

Terraform will create the Azure Virtual Machine and networking resources. Ansible will install Nginx, clone the Mini Finance repository, deploy the website, and verify the deployment.

---

# Task 1 — Create the Project Structure

## Goal

Create separate directories and files for the Terraform infrastructure and Ansible configuration.

### Evidence

#### Screenshot 1 — Terminal or VS Code showing the complete `mini-finance` project structure

![alt text](<Terminal or VS Code showing the complete mini-finance project structure.png>)


### Notes

I created the Mini-Finance project structure, organizing the project into separate terraform and ansible directories to maintain a clear separation between infrastructure provisioning and configuration management. I also created the required Terraform configuration files, Ansible files, README.md, and .gitignore to ensure the project is well-organized, properly documented, and follows good DevOps practices.


# Task 2 — Create the Azure Infrastructure Using Terraform

## Goal

Use Terraform to provision an Ubuntu Virtual Machine with the required Azure networking and security resources.

### Evidence

#### Screenshot 2 — Terraform code showing the `Allow-SSH` rule for port `22` and the `Allow-HTTP` rule for port `80`

![alt text](<Terraform code showing the Allow-SSH rule for port 22 and the `Allow-HTTP rule for port 80.png>)


#### Screenshot 3 — Terraform code showing the association between `nsg-mini-finance` and `nic-mini-finance`

![alt text](<Terraform code showing the association between nsg-mini-finance and nic-mini-finance.png>)

### Notes

I created the Terraform configuration to provision the AWS infrastructure for my Mini-Finance project, including a Virtual Private Cloud (VPC), public subnet, Internet Gateway, route table, Security Group, Elastic IP, and Ubuntu EC2 instance. I configured the security group to restrict SSH access on port 22 to my Ansible controller's public IP address while allowing HTTP traffic on port 80 for public website access. I also configured the route table to enable internet connectivity and associated the security group directly with the EC2 instance to secure access to the server

# Task 3 — Initialize and Apply the Terraform Configuration

## Goal

Format and validate the Terraform configuration, review the execution plan, and provision the Azure infrastructure.

### Evidence

#### Screenshot 4 — End of the `terraform apply` output showing `Apply complete!` with no errors

![alt text](<End of the `terraform apply output showing Apply complete! with no errors.png>)


#### Screenshot 5 — Output of `terraform output public_ip` showing the VM’s public IP address

![alt text](<Output of terraform output public_ip showing the VM’s public IP address.png>)


### Notes

I formatted, initialized, validated, and applied the Terraform configuration to provision the AWS infrastructure for my Mini-Finance project. During deployment, I configured the AWS provider, defined the required networking and security resources, and provisioned an Ubuntu EC2 instance. I also configured an Elastic IP to provide a stable public IP address for SSH access and Ansible automation. Finally, Terraform successfully deployed the infrastructure and returned the EC2 instance's public IP address

# Task 4 — Verify Passwordless SSH Access

## Goal

Confirm that the Ansible controller can connect to the Terraform-provisioned Azure VM using SSH key authentication.

### Evidence

#### Screenshot 6 — Passwordless SSH command and the returned `mini-finance` hostname

![alt text](<passwordless SSH command and the returned mini-finance hostname.png>)

### Notes

I tested passwordless SSH connectivity from my Ansible controller to the AWS EC2 instance using my existing SSH key. The connection was successful, and the instance returned its hostname, confirming that SSH key-based authentication was working correctly and that my Ansible controller could securely access the Mini-Finance server.

# Task 5 — Create the Ansible Inventory and Verify Connectivity

## Goal

Add the Terraform-provisioned Azure VM to the Ansible inventory and confirm that Ansible can connect to it.

### Evidence

#### Screenshot 7 — Ansible ping output showing `SUCCESS` and `pong` from the Azure VM

![alt text](<Ansible ping output showing SUCCESS and pong.png>)


### Configuration File

Copy and paste the complete contents of your `ansible/inventory.ini` file below:

[web]
mini-finance ansible_host=52.203.84.211

[web:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/home/felimek/.ssh/id_ed25519
ansible_python_interpreter=/usr/bin/python3
---

# Task 6 — Create the Multi-Play Ansible Playbook

## Goal

Create one Ansible playbook containing separate plays to install Nginx, deploy the Mini Finance website, and verify the deployment.

### Evidence

#### Screenshot 8 — `site.yml` showing Play 1 and the beginning of Play 2

Screenshot must show:

- Play 1 targeting the `web` group
- Installation of `nginx`, `git`, and `rsync`
- Nginx service configured as started and enabled
- Beginning of Play 2 with the Git repository URL and synchronization task


![alt text](<site.yml showing Play 1 and the beginning of Play 2.png>)

#### Screenshot 9 — `site.yml` showing the deployment destination, handler, and Play 3 verification

Screenshot must show:

- Website destination `/var/www/html/`
- Ownership set to `www-data:www-data`
- Nginx reload handler
- Play 3 targeting `localhost`
- The `uri` verification and `assert` condition

![alt text](<site.yml showing the deployment destination, handler, and Play 3 verification.png>)

### Configuration File

Copy and paste the complete contents of your `ansible/site.yml` file below:

---
# Play 1 — Install and Configure Nginx
- name: Install and Configure Nginx
  hosts: web
  become: true

  tasks:
    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Nginx and Git
      ansible.builtin.apt:
        name:
          - nginx
          - git
        state: present

    - name: Ensure Nginx is started and enabled
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

# Play 2 — Clone and Deploy Mini Finance Site
- name: Deploy Mini Finance Site
  hosts: web
  become: true

  handlers:
    - name: Reload Nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded

  tasks:
    - name: Clone the repository from GitHub
      ansible.builtin.git:
        repo: "https://github.com/pravinmishraaws/mini_finance.git"
        dest: "/tmp/mini_finance"
        version: main
        force: true

    - name: Copy site content to Nginx web root
      ansible.builtin.copy:
        src: "/tmp/mini_finance/"
        dest: "/var/www/html/"
        owner: www-data
        group: www-data
        mode: "0755"
        remote_src: true
      notify: Reload Nginx

# Play 3 — Verify Deployment
- name: Verify Deployment
  hosts: localhost
  connection: local
  gather_facts: false

  tasks:
    - name: Check site returns HTTP 200
      ansible.builtin.uri:
        url: "http://{{ hostvars[groups['web'][0]]['ansible_host'] | default(groups['web'][0]) }}"
        method: GET
        status_code: 200
        return_content: true
        timeout: 10
      register: result
      retries: 6
      delay: 5
      until: result.status == 200

    - name: Assert HTTP 200
      ansible.builtin.assert:
        that:
          - result.status == 200
        success_msg: "Site is up and returning HTTP 200!"
        fail_msg: "Site is NOT returning HTTP 200!"

---

# Task 7 — Validate and Run the Ansible Playbook

## Goal

Validate the syntax of the multi-play Ansible playbook and run it to install Nginx, deploy the Mini Finance website, and verify the deployment.

### Evidence

#### Screenshot 10 — Successful playbook syntax check showing `playbook: site.yml`

![alt text](<Successful playbook syntax check showing playbook site.yml for mini finance.png>)


#### Screenshot 11 — Play 3 output showing the successful HTTP verification and assertion

![alt text](<Play 3 output showing the successful HTTP verification and assertion.png>)


#### Screenshot 12 — Final `PLAY RECAP` showing `failed=0` and `unreachable=0`

![alt text](<Final PLAY RECAP showing failed=0 and unreachable=0.png>)

### Notes

I began by performing an Ansible syntax check to validate site.yml. I then executed the complete multi-play playbook to install and configure Nginx, deploy the Mini Finance website, and verify the deployment.
The final verification play confirmed that the website returned HTTP status code 200 (OK), with zero failed or unreachable hosts.


# Task 8 — Test the Mini Finance Website in a Browser

## Goal

Confirm that the Mini Finance website is publicly accessible through the Azure VM’s public IP address.

### Evidence

#### Screenshot 13 — Mini Finance website successfully loading in the browser, with the Azure VM’s public IP address visible in the address bar


![alt text](<Mini Finance website successfully loading in the browser, with the Azure VM’s public IP address visible in the address bar.png>)


### Website URL

Add your deployed website URL below:


http://52.203.84.211

---

# Task 9 — Create the Project README

## Goal

Create a `README.md` file to document the Mini Finance infrastructure and deployment project.

### Evidence

#### Screenshot 14 — Completed `README.md` displayed in the VS Code Markdown preview or terminal

![alt text](<Completed README.md displayed in the VS Code Markdown preview or terminal.png>)


### README Content

Copy and paste the complete contents of your `README.md` file below:

Mini Finance Deployment on AWS Using Terraform and Ansible

Project Objective

The objective of this project was to provision AWS cloud infrastructure using Terraform and automate the deployment of the Mini Finance website using Ansible.

Tools and Technologies

Amazon Web Services (AWS)

Terraform

Ansible

Ubuntu Linux

Nginx

Git and GitHub

WSL

AWS Infrastructure Created

Terraform provisioned a Virtual Private Cloud (VPC), public subnet, Internet Gateway, route table, security group, EC2 key pair, Ubuntu EC2 instance, and Elastic IP address. The security group allows HTTP traffic on port 80 while restricting SSH access on port 22 to the Ansible controller's public IP address.

Ansible Deployment Process

The Ansible playbook contains three plays:

Installs Nginx and Git and ensures Nginx is started and enabled.

Clones the Mini Finance repository and copies the website files to /var/www/html/.

Verifies that the deployed website returns HTTP status code 200.

Verification Method

I tested connectivity using Ansible's ping module, validated the playbook syntax, and executed the complete deployment playbook. The final verification confirmed HTTP status code 200 OK, with no failed or unreachable hosts.

Challenge and Solution

One challenge was connecting Ansible to the EC2 instance using a passphrase-protected SSH private key. Ansible initially failed because it could not interactively request the key's passphrase. I resolved this by starting the SSH agent and adding my private key using ssh-add. Ansible connectivity was subsequently confirmed with a successful ping response.

I also encountered inventory warnings during the syntax check because I initially ran the command without specifying inventory.ini. I corrected this by including -i inventory.ini in the syntax-check command.

What I Learned

This project strengthened my understanding of Infrastructure as Code, AWS networking, Linux administration, SSH authentication, Ansible configuration management, and automated deployment verification. I learned how Terraform and Ansible work together to provision infrastructure, configure servers, deploy applications, and troubleshoot deployment issues.

# LinkedIn Post Required

## Evidence

#### Screenshot 15 — Published LinkedIn post showing the text and at least one deployment screenshot

![alt text](<Linked Post for mini finance.png>)

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/felix-nwobodo-2a191856_devops-aws-terraform-ugcPost-7517280680737841153-knOF/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE

---

### LinkedIn Submission Notes

**One challenge you faced and how you fixed it:**

One challenge I faced was connecting Ansible to my AWS EC2 instance using a passphrase-protected SSH private key. Ansible initially failed because it could not interactively request the passphrase. I resolved this by starting the SSH agent and adding my private key using ssh-add. I then tested the connection again and confirmed successful connectivity using Ansible's ping module.

**One real-world example where you can use this learning:**

This workflow can be used by a DevOps team to deploy a company's website or business application on AWS. Terraform can provision the cloud infrastructure, while Ansible automatically installs the required software, configures the web server, deploys the application, and verifies that it is accessible. This reduces manual work, improves consistency, and makes deployments easier to repeat.


# Assignment Questions

Answer the following in your own words:

**1. What did you provision using Terraform in this assignment?**

I used Terraform to provision AWS infrastructure, including a Virtual Private Cloud (VPC), public subnet, Internet Gateway, route table, security group, EC2 key pair, Ubuntu EC2 instance, and Elastic IP address. These resources provide the networking, security, and computing environment needed to host the Mini Finance website.


**2. What did Ansible configure and deploy in this assignment?**

I used Ansible to install Git and Nginx, ensure that Nginx was running and enabled at startup, clone the Mini Finance website repository from GitHub, and copy the website files into /var/www/html/. I also created a verification play to check that the deployed website returned a successful HTTP response.


**3. Why is SSH access on port `22` restricted to your public IP address?**

SSH access is restricted to my public IP address to reduce unauthorized access attempts and improve the security of the EC2 instance. This ensures that only connections from my authorized Ansible controller's public IP address can attempt to access the server through SSH.


**4. Why is HTTP port `80` open to the internet?**

HTTP port 80 is open to the internet so that users can access the Mini Finance website through a web browser using the EC2 instance's public IP address. Unlike SSH, which is used for administration, HTTP provides public access to the website.


**5. What is the purpose of the Ansible inventory file?**

The Ansible inventory file identifies the servers Ansible manages and provides connection details such as the target host's IP address, SSH username, and private key path. In this assignment, it tells Ansible how to connect to my AWS EC2 instance to configure and deploy the website.

**6. Why does the playbook use separate plays for install, deploy, and verify?**

The playbook uses separate plays to organize the deployment into clear stages: installing and configuring the required software, deploying the website files, and verifying the result. This structure makes the playbook easier to understand, maintain, troubleshoot, and reuse.


**7. Why is `rsync` useful when deploying website files?**

rsync is useful because it can transfer only new or modified files instead of copying every file during each deployment. This can reduce transfer time and bandwidth usage, particularly for large websites. In my current playbook, I used Ansible's copy module with remote_src: true rather than rsync

**8. What does the Ansible `uri` module verify in this assignment?**

The Ansible uri module sends an HTTP request to the website hosted on the EC2 instance and checks the response. In this assignment, it verifies that the website returns HTTP status code 200, indicating that the server successfully responded to the request. The assert module then confirms whether the verification succeeded.


**9. What issue did you face during this assignment, and how did you fix it?**

I encountered an SSH authentication issue because my private key was protected by a passphrase, and Ansible could not prompt for it during execution. I fixed this by starting the SSH agent and loading my key with ssh-add. I also corrected my syntax-check command to include -i inventory.ini, which allowed Ansible to load the correct inventory and eliminated the inventory warnings.


**10. What did you learn from using Terraform and Ansible together?**

I learned that Terraform and Ansible perform complementary roles in DevOps automation. Terraform provisions and manages cloud infrastructure, while Ansible configures the operating system, installs software, deploys applications, and verifies the outcome. Using both tools together creates a more repeatable and efficient deployment workflow while improving my understanding of AWS networking, Linux administration, SSH security, and automation.


# Required Files

Confirm that the following files are included in your assignment folder:

- [ ] `.gitignore`
- [ ] `README.md`
- [ ] `terraform/providers.tf`
- [ ] `terraform/main.tf`
- [ ] `terraform/variables.tf`
- [ ] `terraform/outputs.tf`
- [ ] `ansible/inventory.ini`
- [ ] `ansible/site.yml`

---

# Submission Instructions

- Add all required screenshots in the correct order.
- Full Name must be visible in required screenshots.
- Add the Azure VM public IP address.
- Add the final Mini Finance website URL.
- Paste `inventory.ini`, `site.yml`, and `README.md` as editable text.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, Azure credentials, subscription IDs, Terraform state contents, or other sensitive information.

---

# Completion Checklist

- [ ] Task 1: `mini-finance` project structure created
- [ ] Task 1: `.gitignore` created
- [ ] Task 2: Terraform Azure infrastructure code created
- [ ] Task 2: `Allow-SSH` rule configured for port `22`
- [ ] Task 2: `Allow-HTTP` rule configured for port `80`
- [ ] Task 2: NSG associated with the Network Interface
- [ ] Task 3: `terraform fmt` completed
- [ ] Task 3: `terraform init` completed
- [ ] Task 3: `terraform validate` completed successfully
- [ ] Task 3: `terraform apply` completed successfully
- [ ] Task 3: `terraform output public_ip` displayed the VM public IP
- [ ] Task 4: Passwordless SSH works from the Ansible controller
- [ ] Task 5: `inventory.ini` created
- [ ] Task 5: Ansible ping returns `SUCCESS` and `pong`
- [ ] Task 6: `site.yml` contains three separate plays
- [ ] Task 6: Play 1 installs Nginx, Git, and rsync
- [ ] Task 6: Play 2 clones and deploys the Mini Finance website
- [ ] Task 6: Play 3 verifies HTTP status code `200`
- [ ] Task 7: Playbook syntax check passes
- [ ] Task 7: Ansible playbook completes successfully
- [ ] Task 7: Final recap shows `failed=0` and `unreachable=0`
- [ ] Task 8: Mini Finance website loads in the browser
- [ ] Task 8: Azure VM public IP is visible in the browser screenshot
- [ ] Task 9: `README.md` completed
- [ ] Screenshots 1–15 are included
- [ ] `inventory.ini`, `site.yml`, and `README.md` are pasted as editable text
- [ ] Assignment questions are answered
- [ ] LinkedIn post published with Anyone visibility
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