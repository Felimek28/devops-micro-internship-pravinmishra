# Assignment 03 — Deploy a Static Website to Multiple Servers Using a Multi-Play Ansible Playbook

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Student Details

**Full Name:** Felix Emeka Nwobodo
**Cloud Platform Used:** AWS 
**Server 1 URL:** `http://107.21.4.231`  
**Server 2 URL:** `http://3.82.188.135`

---

## Purpose

In this assignment, you will create a multi-play Ansible playbook to install Nginx, deploy a static website to two Ubuntu servers, and verify that the website is accessible from both servers.

You may use either AWS EC2 instances or Azure Virtual Machines as your managed servers.

---

# Task 1 — Create the Project Structure

## Goal

Create the required folders and files for the Ansible project.

## Evidence

### Screenshot 1 — Terminal or VS Code showing the complete `static-web` project structure

![alt text](<Terminal or VS Code showing the complete static-web project structure.png>)


# Task 2 — Configure the Ansible Inventory

## Goal

Add both Ubuntu servers to the Ansible inventory.

## Evidence

### Screenshot 2 — Output of `ansible-inventory -i inventory.ini --graph` showing `web1` and `web2`


![alt text](<Output of ansible-inventory -i inventory.ini -graph showing web1 and web2.png>)


## Configuration File

Copy and paste the complete contents of your `inventory.ini` file below:


[web]
web1 ansible_host=107.21.4.231
web2 ansible_host=3.82.188.135

[web:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/home/felimek/.ssh/react-key.pem
ansible_python_interpreter=/usr/bin/python3.10


# Task 3 — Verify Ansible Connectivity

## Goal

Confirm that the Ansible controller can connect to both servers.

## Evidence

### Screenshot 3 — Ansible ping output showing `SUCCESS` and `pong` for both servers

![alt text](<Ansible ping output showing SUCCESS and pong for both servers.png>)


# Task 4 — Download and Personalize the Static Website

## Goal

Download `index.html` to the Ansible controller and personalize the website with your full name.

## Evidence

### Screenshot 4 — Edited `files/index.html` showing the footer line with your full name

![alt text](<Edited files slash index.html showing the footer line with your full name.png>)


# Task 5 — Create the Multi-Play Ansible Playbook

## Goal

Create a single Ansible playbook containing separate plays for installation, deployment, and verification.

## Configuration File

Copy and paste the complete contents of your `site.yml` file below:

---
- name: Install and configure Nginx
  hosts: web
  become: true
  tasks:
    - name: Update the APT package cache
      ansible.builtin.apt:
        update_cache: true

    - name: Install Nginx
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Start and enable Nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

- name: Deploy the static website
  hosts: web
  become: true
  tasks:
    - name: Copy index.html to the web root
      ansible.builtin.copy:
        src: files/index.html
        dest: /var/www/html/index.html
        owner: www-data
        group: www-data
        mode: "0644"
      notify: Reload nginx

  handlers:
    - name: Reload nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded

- name: Verify both websites from the controller
  hosts: localhost
  connection: local
  gather_facts: false
  become: false
  tasks:
    - name: Send an HTTP GET request to each web server
      ansible.builtin.uri:
        url: "http://{{ hostvars[item].ansible_host }}"
        status_code: 200
      loop: "{{ groups['web'] }}"
      register: website_checks

    - name: Confirm each server returned HTTP 200
      ansible.builtin.assert:
        that:
          - item.status == 200
        success_msg: "{{ item.item }} returned HTTP {{ item.status }}"
      loop: "{{ website_checks.results }}"

---

# Task 6 — Validate the Playbook Syntax

## Goal

Check the playbook for YAML or Ansible syntax errors before running it.

## Evidence

### Screenshot 5 — Successful syntax-check output showing `playbook: site.yml`

![alt text](<Successful syntax-check output showing playbook site.yml.png>)


# Task 7 — Run the Multi-Play Playbook

## Goal

Install Nginx, deploy the website, and verify both servers in one playbook run.

## Evidence

### Screenshot 6 — Play 3 verification showing HTTP `200` for both servers

![alt text](<Play 3 verification showing HTTP 200 for both servers.png>)


### Screenshot 7 — Final play recap showing `unreachable=0` and `failed=0` for `web1`, `web2`, and `localhost`

![alt text](<Final play recap showing unreachable=0 and failed=0 for web1, web2, and localhost.png>)


# Task 8 — Verify Idempotency

## Goal

Run the playbook again and confirm that it does not make unnecessary changes.

## Evidence

### Screenshot 8 — Second playbook run showing the play recap with `changed=0`, `unreachable=0`, and `failed=0` for both web servers

![alt text](<Second playbook run showing the play recap with changed=0, unreachable=0, and failed=0 for both web servers.png>)


# Task 9 — Test Both Websites Manually

## Goal

Confirm that the static website is accessible from both public IP addresses.

## Evidence

### Screenshot 9 — `curl -I` output showing HTTP `200 OK` from both servers

![alt text](<curl -I output showing HTTP 200 OK from both servers.png>)


### Screenshot 10 — Browser showing the website from Server 1 with the public IP and your full name visible

![alt text](<Browser showing the website from Server 1 with the public IP and your full name visible.png>)


### Screenshot 11 — Browser showing the website from Server 2 with the public IP and your full name visible

![alt text](<Browser showing the website from Server 2 with the public IP.png>)

![alt text](<Browser showing the website from Server 2 with the public IP and your full name visible.png>)


## Website URLs

Add both deployed website URLs below:


Server 1: http://107.21.4.231
Server 2: http://3.82.188.135



# Task 10 — Complete the Project README

## Goal

Document how the project works and record what you learned.

## README Content

Copy and paste the complete contents of your `README.md` file below:

# Multi-Play Ansible Static Website Deployment

## Project Overview

This project demonstrates how to automate the installation, configuration, and deployment of a static website on two AWS EC2 instances using Ansible.

The playbook is organized into three separate plays. The first play updates the APT package cache, installs Nginx, and ensures the service is running and enabled at boot. The second play deploys the website's index.html file to the web root on both servers using the Ansible Copy module. The third play verifies that both websites are accessible by sending HTTP requests and checking that each server returns status code 200.

## Environment
-Cloud platform: Amazon Web Services (AWS EC2)
-Operating system: Ubuntu Linux on the managed servers; Ubuntu on WSL as the Ansible controller environment
-Number of managed servers:2 (web1 and web2)
-Web server: Nginx
-Configuration management tool: Ansible
-Connection method: SSH
-Website verification: Ansible URI module and HTTP status code checks
-Project files: inventory.ini, site.yml, and files/index.html

## How to Run the Playbook

First, navigate to the project directory and activate the Ansible virtual environment:


cd ~/ansible-onboarding/static-web
source ~/ansible-venv/bin/activate

Check that the playbook syntax is valid:


ansible-playbook -i inventory.ini site.yml --syntax-check


Run the playbook:

ansible-playbook -i inventory.ini site.yml


The playbook performs the following operations:

1.Installs and configures Nginx on both web servers.
2.Copies the static website to /var/www/html/index.html and reloads Nginx when the file changes.
3.Sends HTTP requests from the controller to both servers and asserts that each returns HTTP 200.

Successful execution is confirmed by the final PLAY RECAP, with failed=0 and unreachable=0 for localhost, web1, and web2.

## Issue Faced and Solution

During the assignment, I encountered an SSH connection timeout when running the Ansible ping command against both EC2 instances.

The issue occurred because the AWS security group's inbound SSH rule still allowed connections from my previous public IP address, 129.222.206.201/32, instead of my current IP address.

Solution: I checked my current public IP address and updated the AWS security group's SSH inbound rule to allow traffic from 98.97.76.48/32. After updating the rule, I reran the Ansible ping command and successfully connected to both web servers.

These experiences taught me to distinguish between configuration errors and network connectivity issues and to verify each stage of a deployment systematically.

## What I Learned

Through this assignment, I learned:

How to configure an Ansible inventory to manage multiple AWS EC2 instances.
How to use Ansible ad-hoc commands to test SSH connectivity.
How to structure a playbook with multiple plays and tasks.
How to install packages and manage Linux services using Ansible modules.
How to use privilege escalation with become: true for administrative operations.
How to deploy website files using the Ansible Copy module.
How handlers respond to task notifications and reload services when changes occur.
How to use the URI module and assertions to verify that a website returns HTTP 200.
How AWS security groups and changing public IP addresses can affect remote access.
How to troubleshoot YAML syntax errors, SSH timeouts, and HTTP connectivity problems.
Why deployment verification is important in addition to successful configuration tasks.

Most importantly, I learned that a successful deployment should be verified from the perspective of the service consumer. Installing Nginx does not automatically prove that a website is accessible, so the final verification play provides an additional layer of confidence.



# LinkedIn Post Required

## Evidence

### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/felix-nwobodo-2a191856_devops-aws-ansible-ugcPost-7516927900235870208-8Hke/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE


### Screenshot — Published LinkedIn post

![alt text](<Linkedin post for ansible static web.png>)


# Assignment Questions

Answer the following in your own words:

**1. What issue did you face while completing this assignment, and how did you fix it?**

I encountered SSH connection timeouts when Ansible tried to connect to my AWS EC2 instances. The problem was caused by an outdated public IP address in the security group's inbound SSH rule. I checked my current public IP address and updated the security group to allow SSH access from the correct IP address. I also removed an extra yaml line at the beginning of my playbook that caused a YAML syntax error. After these corrections, the playbook ran successfully, and both websites returned HTTP 200.


**2. What did you learn from this assignment?**

I learned how to use Ansible to automate the installation and configuration of Nginx, deploy a static website to multiple AWS EC2 instances, and verify that the websites are accessible. I also learned how to organize tasks into separate plays, use the copy module to transfer website files, use handlers to reload Nginx when necessary, and troubleshoot SSH connectivity and YAML syntax errors.


**3. Why is it useful to split installation, deployment, and verification into separate plays?**


Splitting installation, deployment, and verification into separate plays makes the playbook easier to understand, manage, and troubleshoot. The installation play ensures Nginx is installed and running, the deployment play copies the website files to the servers, and the verification play checks whether the websites are accessible. This separation makes it easier to identify which stage has a problem and maintain the automation as the project grows


**4. What is one benefit of using the Ansible `copy` module instead of cloning the website directly from Git on every managed server?**

One benefit is that the copy module allows me to manage the website files centrally on my Ansible control machine and distribute them directly to the managed servers. I do not need to install Git or clone the repository separately on every server. This makes deployment simpler, faster, and easier to control.


**5. What does idempotency mean in this assignment?**

Idempotency means that running the same Ansible playbook multiple times should maintain the desired state without making unnecessary changes. For example, once Nginx is installed and running and the correct website file is deployed, running the playbook again should not reinstall or change everything unnecessarily. Ansible checks the current state and only makes changes when needed


**6. What does the Ansible `uri` module verify in Play 3?**

The uri module sends HTTP requests from the Ansible control machine to both web servers to check whether their websites are accessible. In Play 3, it verifies that each website returns the expected HTTP status code 200 OK. The successful checks confirmed that both web1 and web2 were serving their websites correctly.


# Required Files

Confirm that the following files are included in your assignment folder:

- [ ] `inventory.ini`
- [ ] `site.yml`
- [ ] `files/index.html`
- [ ] `README.md`

---

# Submission Instructions

- Add all required screenshots in the correct order.
- Full Name must be visible in required screenshots.
- Include both deployed website URLs.
- Paste `inventory.ini`, `site.yml`, and `README.md` as editable text.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, cloud account IDs, or other sensitive information.

---

# Completion Checklist

- [ ] Task 1: `static-web` folder structure is complete
- [ ] Task 2: Both servers are listed under the `[web]` group in `inventory.ini`
- [ ] Task 2: Inventory graph shows `web1` and `web2`
- [ ] Task 3: Ansible ping returns `SUCCESS` and `pong` for both servers
- [ ] Task 4: `files/index.html` contains your full name
- [ ] Task 5: `site.yml` contains three separate plays
- [ ] Task 5: Play 1 installs, starts, and enables Nginx
- [ ] Task 5: Play 2 deploys `index.html` using the `copy` module
- [ ] Task 5: Nginx reload handler is included
- [ ] Task 5: Play 3 verifies both web servers from the controller
- [ ] Task 6: Playbook syntax check passes
- [ ] Task 7: First playbook run completes with `unreachable=0` and `failed=0`
- [ ] Task 7: URI verification returns HTTP `200` for both servers
- [ ] Task 8: Second playbook run demonstrates idempotency
- [ ] Task 8: Second run shows `changed=0` for both web servers
- [ ] Task 9: Both `curl -I` commands return HTTP `200 OK`
- [ ] Task 9: Website loads from Server 1
- [ ] Task 9: Website loads from Server 2
- [ ] Task 9: Full name is visible on both deployed websites
- [ ] Task 10: `README.md` contains all required explanations
- [ ] Screenshots 1–11 are included
- [ ] `inventory.ini`, `site.yml`, and `README.md` are pasted as editable text
- [ ] Both website URLs are included
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