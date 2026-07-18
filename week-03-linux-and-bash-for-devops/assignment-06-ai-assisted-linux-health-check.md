# Assignment 6 — Build an AI-Assisted Linux Health Check (AI-Assisted Linux Incident Triage)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build a read-only Bash triage script that checks the health of your Ubuntu server and Nginx application, connect it to Claude Code as a reusable `/linux-triage` skill, simulate a controlled Nginx incident, use the skill to gather and analyze evidence, recover the service manually, and verify recovery. The workflow follows the Agentic Loop: Gather → Analyze → Human Act → Verify.

---

# Task 1 — Confirm the Healthy Baseline and Create the Workspace

## Goal

Confirm that Nginx and the React application are healthy before building the automation.

### Evidence

#### Screenshot 1 — Output of `systemctl is-active nginx`, `ss -ltn | grep ':80'`, and `curl -I http://localhost`

![alt text](<systemctl is-active nginx ss -ltn curl.png>)


#### Screenshot 2 — Output of `pwd` and `find . -maxdepth 4 -type d | sort` showing the workspace folder structure

![alt text](<pwd  find maxdepth.png>)


### Notes

Answer the following in your own words:

**1. What proves that Nginx is running?**

When I run systemctl is-active nginx, it returns active. This confirms that Nginx is running.

**2. What proves that the server is listening for HTTP traffic?**


The output of ss -ltn | grep ':80' shows that port 80 is listening. This means the server is ready to receive HTTP requests.


**3. Why must you capture a healthy baseline before simulating an incident?**

Capturing a healthy baseline before simulating an incident is important because it gives you a known-good reference point for comparison. Without a baseline, it's difficult to determine what changed, whether the simulation had the intended effect, or whether recovery was successful.

# Task 2 — Create Project Context and Safety Rules in CLAUDE.md

## Goal

Tell Claude exactly what this project does and what it is not allowed to do.

### Evidence

#### Screenshot 3 — CLAUDE.md open in VS Code showing all four sections (Project Overview, Incident Workflow, Safety Rules, Output Rules)

![alt text](<CLAUDE.md open in VS Code showing all four sections.png>)


### Notes

Answer the following in your own words:

**1. Why should Claude receive project-specific operational rules?**

Claude needs to receive project-specific operational rules so it can generate code, commands, and recommendations that are consistent with my project's standards, workflows, and safety requirements. Without these rules, it may make assumptions that don't match your project's practices.


**2. Why is the human required to execute the recovery command?**

The human is required to execute the recovery command because recovery actions can have significant consequences, especially in production environments. Requiring human approval ensures that someone verifies the situation before making changes to a live system.


**3. Which rule prevents Claude from making an unsupported diagnosis?**

The rule “Do not claim a root cause unless the report contains supporting evidence” prevents Claude from giving a diagnosis that is not supported by the report.


# Task 3 — Use Agentic AI to Plan Before Writing the Script

## Goal

Use Claude Code to inspect the environment and produce a read-only plan before creating any Bash code.

### Evidence

#### Screenshot 4 — Claude Code showing the five-check plan and read-only inspection results

![alt text](<Claude Code showing the five-check plan and read-only inspection results.png>)

### Notes

Answer the following in your own words:

**1. Which part of this task represents the Gather phase?**

The Gather phase is the step where Claude Code inspects the server using read-only commands and creates a health-check plan before writing any Bash script. During this phase, it collects information such as the Nginx service status, whether port 80 is listening, and whether the application is responding. This provides the evidence needed for analysis without making any changes to the system.

**2. Did Claude follow the instruction not to create files? How did you verify this?**

Yes. Claude followed the instruction not to create files because it only inspected the environment and produced a read-only plan without modifying the system or generating any new files.

I verified this by listing the files in the workspace and confirming that no Bash script or other new file was created.


**3. Why is planning before coding useful in DevOps automation?**

Planning before coding is useful in DevOps automation because it helps identify what needs to be automated before any changes are made. It allows you to gather information about the system, define the required checks, and anticipate potential risks. This reduces mistakes, prevents unnecessary changes, and results in a safer and more reliable automation script.

# Task 4 — Build the Linux Triage Bash Script

## Goal

Create one Bash script that gathers consistent Linux and Nginx health evidence.

### Evidence

#### Screenshot 5 — Top section of `linux-triage.sh` showing variables, thresholds, and the checks array

![alt text](<Top section of linux-triage.sh showing variables, thresholds, and the checks array.png>)


#### Screenshot 6 — Middle section showing check functions and conditionals

![alt text](<Middle section showing check functions and conditionals.png>)

#### Screenshot 7 — Bottom section showing the loop, summary function, and exit behavior

![alt text](<Bottom section showing the loop, summary function, and exit behavior.png>)


#### Screenshot 8 — Output of `bash -n scripts/linux-triage.sh` (no syntax errors) and `ls -l scripts/linux-triage.sh` showing executable permission

![alt text](<bash -n scripts and ls -l linux-triage.sh.png>)

### Notes

Answer the following in your own words:

**1. What is stored in the checks array?**

The checks array stores the names of the five functions that check the Nginx service, port 80, HTTP response, disk usage, and available memory.


**2. How does the `for` loop use that array?**

The for loop reads each function name from the array and runs the functions one at a time. This allows the script to complete all five health checks in the given order.


**3. Why are the health checks separated into functions?**

Each function handles one specific check. This makes the script easier to read, test, update, and troubleshoot without affecting the other checks.


**4. What is the purpose of `$(...)` in this script?**

The purpose of $(...) is to run a command and stores its output. For example, the script uses it to collect the timestamp, hostname, HTTP status code, disk usage, available memory, and recent Nginx logs.


**5. Why does the script use different exit codes for HEALTHY, WARN, and FAIL?**

The script uses different exit codes so that both people and automation tools can quickly determine the server's overall health. An exit code of 0 indicates the system is healthy, 1 indicates warnings that need attention but are not critical, and 2 indicates one or more critical failures that require immediate investigation. This makes it easy for monitoring systems, CI/CD pipelines, and other scripts to respond appropriately without having to parse the report.

# Task 5 — Run and Understand the Healthy-State Report

## Goal

Run the Bash script against the healthy server and verify that it creates a report.

### Evidence

#### Screenshot 9 — Output of `./scripts/linux-triage.sh` showing your Full Name and all five check results

![alt text](<.scripts linux-triage.sh showing your Full Name and all five check results.png>)

#### Screenshot 10 — Output showing the captured exit code and final summary

![alt text](<Output showing the captured exit code and final summary.png>)

### Notes

Answer the following in your own words:

**1. What is the overall status of your healthy baseline?**

The overall status of my healthy baseline is HEALTHY. All five health checks passed successfully, with no warnings or failures. Nginx was running, port 80 was listening, the application returned an HTTP 200 response, disk usage was within acceptable limits, and sufficient memory was available. The script returned an exit code of 0, confirming that the server was healthy before any incident simulation.

**2. Which exact Linux evidence proves the application is serving traffic?**

The exact Linux evidence is the successful HTTP health check, where curl returned an HTTP status code of 200. This confirms that the application is not only running but is also successfully serving HTTP requests. The script records this as [PASS] Local HTTP check returned status 200.

**3. Did your script return exit code 0 or 1? Explain why.**

My script returned exit code 0 because all health checks passed successfully. There were no warnings or failures, so the overall status was HEALTHY. According to the script's logic, an exit code of 0 indicates a healthy system, while 1 is used for warnings and 2 is used for failures.

**4. What is the difference between a warning and a failure in this script?**

A warning represents a non-critical issue that does not stop the application from working but requires attention, such as high disk usage or low available memory. A failure represents a critical issue that affects the application's health or availability, such as Nginx being down, port 80 not listening, the HTTP check failing, or disk usage reaching the failure threshold. Warnings result in exit code 1, while failures result in exit code 2.

# Task 6 — Create and Run the /linux-triage Skill

## Goal

Turn the Bash script into a reusable, manually invoked Agentic AI workflow.

### Evidence

#### Screenshot 11 — `SKILL.md` showing the frontmatter, allowed tool restrictions, and safety rules

![alt text](<SKILL.md showing the frontmatter, allowed tool restrictions, and safety rules.png>)

#### Screenshot 12 — `/linux-triage` output for the healthy server

![alt text](<linux-triage output on claude.png>)

### Notes

Answer the following in your own words:

**1. Why does this skill have Bash, Read, and Grep, but not Write?**

The skill needs Bash to run the Linux triage script, Read to open the generated report, and Grep to locate specific PASS, WARN, or FAIL results. It does not need the Write tool because Claude should not create or edit project files during the triage process.


**2. Why is `disable-model-invocation: true` useful for this skill?**

The disable-model-invocation ensures that Claude cannot automatically invoke the /linux-triage skill on its own. Instead, I must explicitly run the skill when I want to perform a server health check. This gives me full control over when inspections are executed, prevents unintended system checks, and ensures the workflow follows the intended human-in-the-loop approach.

**3. What part is performed by Bash, and what part is performed by Claude?**

The Bash script checks Nginx, port 80, the HTTP response, disk usage, available memory, and recent logs. It records the results in linux-health-report.txt.
Claude reads that report, explains the results, identifies warnings or failures, and recommends a safe next step. Claude does not perform the recovery action.


**4. Why is this better than asking Claude "Is my server healthy?" without giving it evidence?**

Using the /linux-triage skill is better than simply asking Claude, "Is my server healthy?" because it provides Claude with real, up-to-date evidence from the server. Without this evidence, Claude would have to rely on assumptions and could not accurately assess the server's condition.

The /linux-triage skill first runs the Bash script to collect current health data, including the Nginx service status, port 80 availability, HTTP response, disk usage, available memory, and recent logs. Claude then analyzes this evidence to produce an accurate, evidence-based assessment and recommend appropriate next steps, rather than making unsupported conclusions.


# Task 7 — Simulate an Nginx Incident and Let the Skill Diagnose It

## Goal

Create a controlled service failure, gather evidence through Bash, and let Claude analyze the evidence without taking recovery action.

### Evidence

#### Screenshot 13 — Output showing Nginx is inactive and the HTTP request fails

![alt text](<Output showing Nginx is inactive and the HTTP request fails.png>)

#### Screenshot 14 — `/linux-triage` output showing failed evidence, most likely cause, and a suggested recovery command

![alt text](<linux-triage output showing failed evidence, most likely cause, and a suggested recovery command.png>)

#### Screenshot 15 — `incident-failure-report.txt` showing the failed checks and your Full Name

![alt text](<incident-failure-report.txt showing the failed checks and your Full Name.png>)

### Notes

Answer the following in your own words:

**1. Which three checks failed?**

The Nginx service check, port 80 check, and local HTTP check failed. The disk and memory checks were not affected by stopping Nginx.


**2. What evidence supports the conclusion that Nginx is unavailable?**

The report contains several pieces of evidence showing that Nginx is unavailable. First, the service check reports [FAIL] Nginx service is not active, confirming that the Nginx service is not running. Second, [FAIL] Port 80 is not listening shows that the server is no longer accepting HTTP connections on the expected port. Third, the local HTTP health check returned status 000, indicating that curl could not establish a connection to the web server. Finally, the recent system logs show that Nginx was stopped successfully (Stopped nginx.service), which explains why the service, port, and HTTP checks all failed. Together, these findings provide consistent evidence that Nginx is unavailable.

**3. Did Claude execute the recovery command? Why is that important?**

No, Claude did not execute the recovery command. Instead, it analyzed the evidence collected by the Bash script, identified that Nginx was unavailable, and recommended the appropriate recovery action. The actual recovery command had to be executed manually by me.

**4. Which phase of the Agentic Loop is represented by the Bash report?**

The Bash report represents the Gather phase of the Agentic Loop. During this phase, the Bash script collects factual, read-only evidence about the system by checking the Nginx service status, port 80, the local HTTP response, disk usage, available memory, and recent Nginx logs. It records this information in linux-health-report.txt, providing the evidence that Claude uses in the Analyze phase to assess the system's health and recommend the appropriate next steps


**5. Which phase is represented by Claude's explanation?**

Claude's explanation represents the Analyze phase of the Agentic Loop. After the Bash script gathers evidence and generates the health report, Claude interprets the results, explains the overall system status, identifies any warnings or failures, and recommends appropriate next steps based on the collected evidence

# Task 8 — Recover Manually, Verify Again, and Write the Incident Summary

## Goal

Recover the service as the human operator and prove that the system is healthy again.

### Evidence

#### Screenshot 16 — Output showing Nginx is active and `curl -I http://localhost` returns 200 OK

![alt text](<Output showing Nginx is active and curl -I http returns 200 OK.png>)

#### Screenshot 17 — Second `/linux-triage` output showing successful recovery with no FAIL results

![alt text](<linux-triage` output showing successful recovery with no FAIL results.png>)

#### Screenshot 18 — Output of `ls -lah reports` showing both `incident-failure-report.txt` and `recovery-report.txt`

![alt text](<ls -lah reports showing both incident-failure-report.txt and recovery-report.txt.png>)


#### Screenshot 19 — `incident-summary.md` showing all required sections and your Full Name

![alt text](<incident-summary.md showing all required sections and your Full Name-1.png>)


### Notes

Answer the following in your own words:

**1. What action did you execute manually?**

After reviewing the evidence and Claude’s recommendation, I manually start nginx by running the command:
sudo systemctl start nginx


**2. What evidence proves that the service recovered?**

The systemctl is-active nginx command returned active, and the local HTTP request returned HTTP/1.1 200 OK. The second /linux-triage run also showed that the service, port, and HTTP checks passed.


**3. Why is the second triage run necessary?**

Starting Nginx does not automatically prove that the complete application is healthy. The second triage run checks the service, port, HTTP response, disk, and memory again to confirm that the server returned to a healthy state.



**4. What could go wrong if an AI agent automatically restarted every failed service?**

A failed service may have a configuration problem, resource issue, dependency failure, or another serious cause. Automatically restarting every service could hide the real problem, create a restart loop, or make the incident worse. The evidence should be reviewed before taking action

**5. In one sentence, explain the difference between using AI as a chatbot and using AI in this agentic workflow.**

A chatbot only answers my question, but in this agentic workflow, Claude uses tools to gather and analyze real server evidence while I remain responsible for approving and performing the recovery action

# Incident Summary

Fill in all seven sections below in your own words.

**Full Name:** Felix Emeka Nwobodo

**Date:** 18/07/2026

---

**1. Reported Symptom**

The Nginx web server became unavailable after the service was stopped manually. As a result, the application could not accept HTTP requests, and attempting to access http://localhost returned a connection error instead of a web page.

**2. Evidence Collected**

The Bash triage script collected the following evidence:

[FAIL] Nginx service is not active – confirmed that the Nginx service was not running.
[FAIL] Port 80 is not listening – showed that no process was accepting HTTP connections on port 80.
[FAIL] Local HTTP check returned status 000 – indicated that curl could not connect to http://localhost.
Recent Nginx service logs showed:
Stopping nginx.service
nginx.service: Deactivated successfully
Stopped nginx.service
The script also confirmed that the underlying server resources were healthy:
[PASS] Root disk usage is 64%
[PASS] Available memory is 349 MB

**3. Most Likely Cause**

Based on the collected evidence, the most likely cause was that the Nginx service had been stopped. This conclusion is supported by the service status, the absence of a listener on port 80, the failed HTTP health check, and the system logs showing that nginx.service was stopped successfully. There was no evidence of disk or memory resource exhaustion.

**4. Human-Approved Recovery Action**
After reviewing Claude's recommendation, I manually executed the following recovery command:

sudo systemctl start nginx

The recovery action was performed manually to maintain human control over changes to the running system.

**5. Verification**

The recovery was verified using the following evidence:

systemctl is-active nginx returned active, confirming that the Nginx service was running.
curl -I http://localhost returned HTTP/1.1 200 OK, confirming that the application was serving HTTP requests successfully.
A new health report (recovery-report.txt) confirmed that the service had returned to a healthy state.


**6. Safety Decision**

The AI skill was allowed to gather and analyze evidence because these actions were read-only and posed no risk to the server. However, it was not allowed to restart the service because recovery actions can affect system availability. Keeping the recovery step under human control follows the human-in-the-loop approach, ensuring that operational changes are intentional, reviewed, and authorized before they are executed

**7. Agentic Loop Mapping**

Gather: The Bash script collected read-only evidence by checking the Nginx service status, port 80, HTTP response, disk usage, available memory, and recent service logs.
Analyze: Claude reviewed the health report, identified the failed checks, explained the most likely cause based on the evidence, and recommended a safe recovery command.
Human Act: I reviewed Claude's recommendation and manually restarted the Nginx service using sudo systemctl start nginx.
Verify: I confirmed the recovery by verifying that systemctl is-active nginx returned active and curl -I http://localhost returned HTTP/1.1 200 OK, proving that the application was serving traffic again.

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

<<<<<<< HEAD:week-03-linux-for-devops/assignment-06-ai-assisted-linux-health-check.md
https://www.linkedin.com/posts/felix-nwobodo-2a191856_devops-linux-bash-ugcPost-7484039694201044993-NZbS/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE
=======
`Add your URL here`

---
>>>>>>> upstream/main:week-03-linux-and-bash-for-devops/assignment-06-ai-assisted-linux-health-check.md

#### Screenshot — Published LinkedIn post

![alt text](<Linked Post for assignment 6.png>)

# GitHub Repository URL

Paste the URL of your GitHub folder or repository containing the assignment files here:

<<<<<<< HEAD:week-03-linux-for-devops/assignment-06-ai-assisted-linux-health-check.md
https://github.com/Felimek28/devops-micro-internship-pravinmishra.git
=======
`Add your URL here`

---
>>>>>>> upstream/main:week-03-linux-and-bash-for-devops/assignment-06-ai-assisted-linux-health-check.md

# Submission Instructions

- Add all required screenshots in your submission
- Full Name must be visible in required screenshots and the Bash report
- All written answers must be in your own words
- Do not expose sensitive information (keys, passwords, AWS account IDs, tokens)
- GitHub URL must be included in this document

---

# Completion Checklist

- [ ] Task 1: Healthy baseline confirmed, workspace created (Screenshots 1–2, Notes answered)
- [ ] Task 2: CLAUDE.md created with all four sections (Screenshot 3, Notes answered)
- [ ] Task 3: Five-check plan produced by Claude using read-only tools (Screenshot 4, Notes answered)
- [ ] Task 4: `linux-triage.sh` created, syntax validated, executable permission set (Screenshots 5–8, Notes answered)
- [ ] Task 5: Healthy-state report generated with no FAIL result (Screenshots 9–10, Notes answered)
- [ ] Task 6: `/linux-triage` skill created and run successfully on healthy server (Screenshots 11–12, Notes answered)
- [ ] Task 7: Nginx incident simulated, failed evidence captured, Claude did not execute recovery (Screenshots 13–15, Notes answered)
- [ ] Task 8: Nginx recovered manually, recovery verified, reports saved, incident summary complete (Screenshots 16–19, Notes answered)
- [ ] Incident summary contains all seven required sections
- [ ] LinkedIn post published and URL submitted
- [ ] Full Name visible in all required screenshots and the Bash report
- [ ] Skill does not have Write permission
- [ ] Skill did not execute any recovery commands
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