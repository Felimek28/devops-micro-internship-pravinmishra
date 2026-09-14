# Assignment 7 — AI-Assisted AWS Security and Cost Audit

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will build a read-only Bash script that audits the AWS resources you deployed earlier this week — your S3 static site, EC2 instance(s), security groups, RDS database, and EBS volumes — for common security and cost misconfigurations.

You will then connect that script to Claude Code as a reusable `/aws-audit` skill that explains what it found and recommends a fix, without ever making the fix itself.

Finally, you will find a real misconfiguration in your own account, apply the fix yourself, and prove it worked with a second audit run.

---

# Task 1 — Confirm Your AWS Resources and Set Up Your Workspace

## Goal

Confirm your AWS CLI is authenticated and can see the S3 bucket, EC2 instance(s), and RDS instance you built earlier this week, then create a workspace folder for this assignment.

### Evidence

#### Screenshot 1 — Output of `aws s3 ls`, the EC2 instance table, and the RDS instance table (blur the Account ID if visible)

![alt text](<s3 ec2 rds.png>)


#### Screenshot 2 — Output of `pwd` and `find . -maxdepth 4 -type d | sort`


![alt text](<Output of pwd and find maxdepth 4 type d sort.png>)


### Notes You Must Write (Very Important)

**1. Which resources from this week's earlier assignments did you see in the listings?**

 EC2 instances, RDS, S3 Buckets.

**2. Why must you confirm your resources exist before writing an audit script against them?**

You must confirm that the resources exist before writing an audit script because the script depends on accurate resource names, IDs, configurations, and relationships between resources.

If you assume that resources exist or use incorrect names and IDs, the audit may fail, miss important resources, or produce misleading results. 

Confirming the resources first ensures that the audit script is built against the actual AWS environment and can produce reliable and meaningful results.

# Task 2 — Define Safety Rules in CLAUDE.md

## Goal

Create a `CLAUDE.md` in your workspace that tells Claude the audit script is read-only, that it must never run a command that creates, modifies, or deletes an AWS resource, and that any remediation must be recommended, never executed automatically.

### Evidence

#### Screenshot 3 — `CLAUDE.md` open in VS Code showing all four sections


![alt text](<CLAUDE.md open in VS Code showing all four sections.png>)


### Notes You Must Write (Very Important)

**1. Why should Claude never be given permission to run `revoke-security-group-ingress` itself, even if the fix is obviously correct?**

Claude should never be given permission to execute revoke-security-group-ingress because changing security group rules can unintentionally disrupt application connectivity or weaken security. Claude can analyze the evidence and recommend the correct remediation, but a human should review and approve the change before execution to prevent accidental or incorrect modifications.



**2. Which rule prevents Claude from claiming a finding that the report does not support?**
This is the rule
“Do not claim a finding unless the report contains supporting evidence.”

This ensures Claude bases its findings only on verified evidence in the report and does not make unsupported assumptions or claims.


# Task 3 — Plan the Audit with Claude Code

## Goal

Ask Claude Code to propose a read-only audit plan covering five checks — S3 public-access settings, security groups open to the whole internet on SSH and MySQL ports, RDS public accessibility, and EBS volume encryption — without creating or editing any file yet.

### Evidence

#### Screenshot 4 — Claude Code showing the five-check plan


![alt text](<Claude Code showing the five-check plan.png>)


### Notes You Must Write (Very Important)

**1. Which part of this task represents the Gather phase?**

The Gather phase is represented by running the aws-audit.sh Bash script. The script collects objective evidence from the AWS environment, such as Security Group rules and other security or cost-related configuration details, and generates an audit report containing the PASS, WARN, and FAIL findings.

This phase focuses only on collecting evidence, not interpreting or changing it.


**2. Did every proposed command start with `describe-`, `get-`, or `list-`? Why does that matter?**

Yes. The proposed commands used read-only AWS CLI operations, such as commands beginning with describe-, get-, or list-.

This matters because these commands retrieve information without modifying AWS resources. It ensures the audit remains safe and read-only during the Gather phase. Any command that changes resources, such as delete-, modify-, or revoke-, would require a separate human-approved remediation step.


# Task 4 — Build the AWS Audit Script

## Goal

Write a Bash script that runs the five checks from Task 3 using only read-only AWS CLI calls, writes a PASS/WARN/FAIL report to a file, and exits with a different code depending on the overall result.

Make it executable and confirm it has no syntax errors.

### Evidence

#### Screenshot 5 — Top section of `aws-audit.sh` showing the variables and the checks array


![alt text](<Top section of aws-audit.sh showing the variables and the checks array.png>)


#### Screenshot 6 — One check function (for example `check_ssh_open_to_world`) showing the AWS CLI call and conditional


![alt text](<One check function for example check_ssh_open_to_world showing the AWS CLI call and conditional.png>)


#### Screenshot 7 — Output of `bash -n scripts/aws-audit.sh` and `ls -l scripts/aws-audit.sh`

![alt text](<Output of bash -n scripts aws-audit.sh and ls -l aws-audit.sh.png>)


### Notes You Must Write (Very Important)

**1. What is stored in the checks array, and how does the loop use it?**

The checks array stores the names of the five audit check functions: S3 public access, SSH exposure, MySQL exposure, RDS public access, and EBS encryption. The for loop goes through each function name in the array and executes it one by one, making it easy to run all audit checks in a defined order.


**2. Why does every AWS CLI call in this script use `--query` and `--output text` instead of parsing raw JSON?**

--query extracts only the specific information required for each audit check, while --output text converts the result into a simple text value that Bash can easily store in a variable and evaluate with conditional statements such as if. This makes the script simpler, more readable, and easier to automate, while avoiding the need to manually parse large raw JSON responses.



**3. Why does the script use different exit codes for HEALTHY, WARN, and FAIL?**

Different exit codes allow the audit result to be interpreted automatically by other tools, scripts, or CI/CD pipelines. The script returns 0 for HEALTHY, 1 for WARN, and 2 for FAIL, allowing automation to quickly determine the overall security status without having to read or parse the full audit report.


# Task 5 — Run the Baseline Audit

## Goal

Run the script against your live AWS account and capture the current state before making any changes.

### Evidence

#### Screenshot 8 — Output of `./scripts/aws-audit.sh` showing your Full Name and all five checks


![alt text](<Output of dot slash scripts slash aws-audit.sh showing your Full Name and all five checks.png>)


#### Screenshot 9 — Output showing the captured exit code and final summary

![alt text](<Output showing the captured exit code and final summary.png>)

### Notes You Must Write (Very Important)

**1. What is the overall status of your baseline audit?**

The overall status of my baseline audit is FAIL. The audit summary shows 2 PASS, 1 WARN, and 2 FAIL, with a Script Exit Code of 2.

**2. Did any check return FAIL or WARN? If so, which one, and what evidence did it show?**

Yes. The audit returned two FAIL findings and one WARN finding:

FAIL – S3 Public ACLs: The S3 bucket pravin-portfolio-felix-emeka-nwobodo-us-east-1 does not fully block public ACLs. The evidence showed BlockPublicAcls=False and IgnorePublicAcls=False.
FAIL – SSH Open to the World: One security group allows SSH traffic on port 22 from 0.0.0.0/0, meaning SSH is accessible from any IPv4 address on the internet.
WARN – EBS Encryption: Two EBS volumes are not encrypted.



**3. If every check passed, what does that tell you about the security posture of your account so far?**

If every check passed, it would indicate that the AWS resources reviewed by the audit are following the security controls defined in the script. For example, there would be no publicly accessible RDS instance, no SSH or MySQL access open to the entire internet, public S3 ACLs would be blocked, and EBS volumes would be encrypted. This would suggest a healthy security posture for the areas tested, although it would not guarantee that the entire AWS account is completely secure because the audit only checks specific controls.

# Task 6 — Build and Run the /aws-audit Skill

## Goal

Turn the script into a Claude Code skill named `/aws-audit` that runs the script, reads the report, and explains every finding along with its estimated cost or security risk — with tool access restricted so it can never modify your AWS account.

### Evidence

#### Screenshot 10 — `SKILL.md` showing the frontmatter, tool restrictions, and safety rules

![alt text](<SKILL.md  showing the frontmatter, tool restrictions, and safety rules.png>)


#### Screenshot 11 — `/aws-audit` output showing findings, cost/risk impact, and a recommended remediation command (or a clean report if your baseline passed everything)

![alt text](<slash aws-audit output showing findings, cost , risk.png>)


### Notes You Must Write (Very Important)

**1. Why does this skill have Bash, Read, and Grep, but not Write?**

The skill is intentionally designed to be read-only and safe. Bash is used to execute the AWS audit script, while Read and Grep allow the skill to inspect configuration files, logs, and audit reports. Write is not included because the skill should not modify files or make changes to AWS resources. This enforces a clear separation between evidence gathering and remediation, ensuring that any corrective action requires explicit human approval.


**2. What part is performed by Bash, and what part is performed by Claude?**

Bash performs the evidence-gathering part by executing the aws-audit.sh script and collecting objective audit results from the AWS environment.

Claude performs the analysis and decision-support part. It reads the generated report, identifies WARN and FAIL findings, interprets the evidence, assesses the potential cost or security risk, and recommends a remediation command for the human to review. Claude does not execute the remediation; the final action remains under human approval and control.


**3. Why is estimating cost/risk impact something the AI adds on top of a plain PASS/FAIL script?**

A plain PASS/FAIL script can identify whether a specific check succeeded or failed, but it provides limited context about the severity, potential cost, or operational impact of the finding.

Claude adds value by interpreting the audit evidence and explaining whether a finding could result in direct monthly costs, security exposure, operational risk, or compliance/audit concerns. It can also help prioritize findings based on their potential impact. This gives the human a clearer understanding of what matters most and why before deciding whether remediation is necessary.


# Task 7 — Fix a Real Finding and Re-Verify

## Goal

Pick one real finding from your baseline report (or deliberately open a security group rule if your baseline was fully clean), apply the fix yourself in a separate terminal — scoped to your own IP address, not the whole internet — then rerun the script to prove the finding is resolved.

### Evidence

#### Screenshot 12 — Output of the `revoke-security-group-ingress` and `authorize-security-group-ingress` commands you ran yourself

![alt text](<Output of the revoke-security-group-ingress.png>)

![alt text](<Output of the authorize-security-group-ingress commands you ran yourself.png>)

#### Screenshot 13 — Rerun of `./scripts/aws-audit.sh` showing the finding is now PASS

![alt text](<Rerun of slash scripts slash aws-audit.sh showing the finding is now PASS.png>)


### Notes You Must Write (Very Important)

**1. Which exact finding did you fix, and what command did you run?**

I fixed an EC2 Security Group finding that allowed SSH access on port 22 from 0.0.0.0/0, which exposed the instance to unnecessary internet-wide access.

I first removed the unrestricted SSH rule and then added a new SSH rule restricted to my own public IP address. This reduced the attack surface while preserving the SSH access I needed.

This was performed manually by me after reviewing the AI-generated recommendation, keeping the workflow human-approved and controlled.


**2. Why did you scope the new rule to your own IP address instead of leaving it open to `0.0.0.0/0`?**

I scoped the SSH rule to my own public IP address , which allows only that specific IP address to connect to port 22. This follows the principle of least privilege by limiting SSH access to only the source that requires it.

Leaving the rule open to 0.0.0.0/0 would allow SSH connection attempts from anywhere on the internet, significantly increasing the attack surface and the risk of unauthorized access.


**3. Did Claude execute the remediation command, or did you? Why does that matter?**

I executed the remediation command myself after reviewing and approving Claude’s recommendation. Claude did not execute the change.

This matters because it maintains human-in-the-loop control. Claude can analyze the evidence, identify risks, and recommend a remediation, but the human makes the final decision and authorizes any change to the AWS environment. This reduces the risk of unintended or destructive changes while keeping AI-assisted remediation safe and auditable.


**4. Which phase of the Agentic Loop does the Bash script represent? Which phase does Claude's explanation represent? Which phase is you running the fix?**

The Bash audit script represents the Gather phase because it collects objective evidence from the AWS environment.

Claude’s explanation and analysis represent the Analyze phase because Claude interprets the audit results, identifies risks, estimates potential impact, and recommends a remediation.

Me running the fix represents the Human Act phase because I reviewed Claude’s recommendation, approved it, and manually applied the remediation.

After the change, the audit is run again to Verify that the issue has been resolved.

In summary: Gather → Analyze → Human Act → Verify.


# LinkedIn Post (Required)

## Goal

Create a LinkedIn post including:

- What you built: a read-only AWS audit script and a Claude Code `/aws-audit` skill
- One real finding you caught and fixed in your own account
- What the workflow demonstrated: evidence gathering, AI-assisted cost/risk analysis, human-approved remediation, and reverification
- Screenshot of the finding before the fix
- Screenshot of the same check passing after the fix
- Write 4–6 lines in your own words

Suggested tags:

`#DMIByPravinMishra #AWS #AgenticAI #ClaudeCode #DevOps`

### Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/felix-nwobodo-2a191856_dmibypravinmishra-aws-agenticai-activity-7505005063245869056-kX0P?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE


#### Screenshot of Published LinkedIn Post

![alt text](<Linkedin post for Agentic Ai assisted check.png>)

# Submission Instructions

Complete all tasks in sequence.

Your submission must include:

- All 13 required task screenshots
- Answers to every **Notes You Must Write** question
- `CLAUDE.md`
- `scripts/aws-audit.sh`
- `.claude/skills/aws-audit/SKILL.md`
- `reports/aws-audit-report.txt` baseline report and the reverified report from Task 7
- GitHub folder or repository URL containing the assignment files
- Your Full Name visible in the required outputs
- LinkedIn post URL
- Screenshot of the published LinkedIn post
- GitHub repository URL (containing all assignment files)

---

# Completion Checklist

- [ ] Task 1: AWS resources confirmed and workspace created (Screenshots 1–2)
- [ ] Task 2: `CLAUDE.md` created with project context and safety rules (Screenshot 3)
- [ ] Task 3: Claude produced a read-only five-check audit plan before any script existed (Screenshot 4)
- [ ] Task 4: `aws-audit.sh` built, executable, and passes `bash -n` (Screenshots 5–7)
- [ ] Task 5: Baseline audit captured and saved with Full Name visible (Screenshots 8–9)
- [ ] Task 6: `/aws-audit` skill loads and runs successfully with no Write permission (Screenshots 10–11)
- [ ] Task 7: A real finding was fixed by you and reverified as PASS (Screenshots 12–13)
- [ ] Skill never executed a remediation command
- [ ] New security group rule is scoped to your own IP, not `0.0.0.0/0`
- [ ] All 13 required task screenshots are included
- [ ] All "Notes You Must Write" questions are answered in your own words
- [ ] No AWS credentials or unblurred account IDs exposed
- [ ] LinkedIn post published and URL submitted
- [ ] GitHub repository URL included in submission
- [ ] All assignment files committed and visible in GitHub repository

---

# Final Submission

Submit your GitHub repository URL containing all assignment files, screenshots, reports, and output.

### GitHub Repository URL

Paste your GitHub repository URL here:

`Add your GitHub repository URL here`

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