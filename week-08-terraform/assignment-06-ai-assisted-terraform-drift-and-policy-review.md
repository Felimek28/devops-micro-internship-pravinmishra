# Assignment 6 — AI-Assisted Terraform Drift and Policy Review

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Student Details

**Full Name:** Felix Emeka Nwobodo 
**GitHub Repository/Folder URL:** Add your GitHub URL here

---

## Purpose

Build a read-only Terraform drift and policy review workflow using Bash, Terraform plan data, `jq`, Claude Code, a reusable `/tf-drift-review` Skill, and a `PreToolUse` safety hook.

The workflow must follow this pattern:

```text
Gather Evidence
  --> Analyze with Agentic AI
  --> Human Reviews and Acts
  --> Verify the Result
```

The `/tf-drift-review` Skill and `tf-drift-check.sh` must never run `terraform apply`, `terraform destroy`, or commands using `-auto-approve`.

---

# Task 1 — Confirm the Clean Baseline and Create the Workspace

## Goal

Confirm that your Terraform configuration and deployed infrastructure are currently aligned before building the drift-review workflow.

## Evidence

### Screenshot 1 — Clean Terraform Plan

Add a screenshot of `terraform plan` showing no pending changes.

![alt text](<terraform plan for AI .png>)


### Screenshot 2 — Assignment Workspace

Add a screenshot of the folder structure showing `AI Assignment/`, `reports/`, and the Terraform project.


![alt text](<folder structure showing AI Assignment, reports  and the Terraform project.png>)


## Questions

### 1. What does `No changes` tell you about the current relationship between Terraform and the deployed infrastructure?

The No changes result confirms that Terraform is in sync with the deployed AWS infrastructure. The resources currently deployed match the desired configuration defined in the Terraform code, so Terraform does not need to create, modify, or destroy any resources



### 2. Why is a clean baseline important before introducing a test change?

A clean baseline provides a known and stable starting point. When Terraform reports No changes, any difference detected after introducing a test change can be confidently attributed to that change. This makes the test easier to validate, troubleshoot, and roll back


# Task 2 — Create Project Context and Safety Rules in `CLAUDE.md`

## Goal

Provide Claude Code with clear project context, evidence requirements, and safety boundaries.

## Evidence

### Screenshot 3 — Project Context and Safety Rules

Add a screenshot of `CLAUDE.md` open in VS Code showing the Project Overview, Review Workflow, Safety Rules, and Output Rules.


![alt text](<CLAUDE md open in VS Code showing the Project Overview, Review Workflow, Safety Rules, and Output Rules..png>)



## Questions

### 1. Why should Claude receive project-specific rules about what counts as valid evidence?

Claude should receive project-specific evidence rules so that it understands exactly what constitutes valid proof for the project. This reduces ambiguity and prevents it from treating configuration or assumptions as evidence of a successful deployment. Clear evidence rules help Claude validate the actual deployed infrastructure against the project requirements.


### 2. Why must the human remain responsible for running `terraform apply`?

The human must remain responsible for running terraform apply because it performs real changes to infrastructure and can create costs, modify production resources, or cause service disruption. Claude can assist with analysis, planning, and validation, but the human should review the proposed changes and make the final decision to apply them. This provides a human approval and accountability step in the Agentic AI workflow



### 3. Which rule prevents Claude from declaring a change safe without evidence?


The evidence-based validation rule prevents Claude from declaring a change safe without proof. Claude must verify the actual result using appropriate evidence, such as Terraform plan/apply output, AWS resource state, or application tests, before claiming that the change was successful or safe



# Task 3 — Build the Terraform Drift and Policy Check Script

## Goal

Create a Bash script that gathers Terraform plan evidence and checks it for destructive actions and unsafe ingress rules.

## Evidence

### Screenshot 4 — Script Variables and Checks Array

Add a screenshot of the top section of `tf-drift-check.sh` showing the variables and `checks` array.

![alt text](<top section of tf-drift-check.sh showing the variables and checks array..png>)


### Screenshot 5 — Destructive-Action and Open-Ingress Checks

Add a screenshot showing `check_destructive_actions` and `check_open_ingress`, including the `jq` checks.


![alt text](<check destructive actions and check open ingress.png>)



### Screenshot 6 — Script Validation and Permissions

Add a screenshot showing successful `bash -n` and `ls -l` output.

![alt text](<successful bash -n and  ls -l output.png>)

## Questions

### 1. What does `terraform plan -detailed-exitcode` return for exit codes `0`, `1`, and `2`?

0 = Nothing to change ✅
1 = Something went wrong ❌
2 = Changes detected 🔄


### 2. Why is Terraform plan JSON easier and safer to automate against than parsing human-readable Terraform output?

Terraform plan JSON is easier and safer to automate against because it provides structured, machine-readable data, while human-readable output is designed for people.


### 3. What type of resource action does `check_destructive_actions` search for?

check_destructive_actions looks for delete/destroy actions, especially resources that may be replaced or permanently removed.

### 4. Why does finding a `delete` action also help detect replacements?

Because Terraform often performs a replacement by first destroying the existing resource and then creating a new one.


### 5. Why must this script never run `terraform apply`?

The script must never run terraform apply because apply makes real changes to infrastructure. It can create, modify, or delete resources, potentially causing downtime, data loss, security issues, or unexpected costs.
AI can recommend and validate; the human remains responsible for executing terraform apply

# Task 4 — Run the Script Against the Clean Baseline

## Goal

Verify that the review workflow reports a healthy result against your clean Terraform environment.

## Evidence

### Screenshot 7 — Healthy Baseline Report

Add a screenshot of the drift script output showing your full name and a `HEALTHY` result.

![alt text](<drift script output showing your full name and a HEALTHY result.png>)


### Screenshot 8 — Baseline Script Exit Code

Add a screenshot showing the captured script exit code `0`.

![alt text](<showing the captured script exit code 0.png>)


## Questions

### 1. What is the Overall Status of your baseline?

The overall status of my baseline was HEALTHY. This means the Terraform configuration was aligned with the deployed infrastructure, and the drift-review workflow did not detect any pending, destructive, or unsafe changes


### 2. Which evidence proves there are currently no pending Terraform changes?

The evidence that proves there are currently no pending Terraform changes is the Terraform plan output, which states, “No changes. Your infrastructure matches the configuration.” Additionally, the drift-check script returned a Terraform detailed exit code of 0 and an Overall Status of HEALTHY. Together, these results confirm that the deployed infrastructure matches the Terraform configuration and that there are no pending changes to apply.



### 3. Was `reports/tfplan.json` created? Explain why or why not.

No, reports/tfplan.json was not created. During the clean baseline check, terraform plan -detailed-exitcode returned exit code 0, indicating that there were no pending changes. The script is designed to generate tfplan.json only when Terraform returns exit code 2, which means changes are pending and require further analysis. Therefore, because the baseline was clean, there was no pending plan to convert to JSON, and reports/tfplan.json was not created.


# Task 5 — Create and Run the `/tf-drift-review` Claude Code Skill

## Goal

Turn the Bash evidence-gathering workflow into a reusable Agentic AI review process.

## Evidence

### Screenshot 9 — `/tf-drift-review` Skill Configuration

Add a screenshot of `SKILL.md` showing the frontmatter, allowed tools, and safety rules.

![alt text](<SKILL.md showing the frontmatter, allowed tools, and safety rules..png>)


### Screenshot 10 — Clean Agentic AI Review

Add a screenshot of `/tf-drift-review` showing the clean `HEALTHY` result.

![alt text](<Run the slash tf-drift-review Claude Code Skill.png>)


## Questions

### 1. Why does this Skill have `Bash`, `Read`, and `Grep`, but not `Write`?

The Skill has Bash, Read, and Grep, but not Write because it is designed to inspect and validate the Terraform project without modifying files.

Bash allows it to run safe commands such as terraform plan.
Read allows it to inspect configuration and report files.
Grep allows it to search for specific Terraform settings or potential policy issues.
No Write permission prevents the Skill from directly modifying Terraform configuration or other project files.


### 2. Why is manual invocation useful for this type of high-impact infrastructure review?

Manual invocation is useful for a high-impact infrastructure review because it ensures that the review is deliberately initiated by a human when needed, rather than running automatically and potentially triggering actions at an inappropriate time. It keeps the human in control of the review process while allowing the AI to inspect Terraform configuration, analyze evidence, and identify potential risks before any infrastructure changes are made.


### 3. Which part of the workflow is deterministic Bash automation?

The deterministic Bash automation is the tf-drift-check.sh script itself. It consistently runs terraform plan -detailed-exitcode, evaluates the exit code, performs the destructive-action and open-ingress checks, counts PASS/WARN/FAIL results, and generates the final report.

This part is deterministic because it follows predefined commands and rules, rather than making decisions based on AI judgment.



### 4. Which part requires Claude's reasoning?

The part that requires Claude’s reasoning is the analysis and interpretation of the evidence produced by the Bash automation. Claude reviews the Terraform plan, configuration, and policy-check results to determine what the changes mean, identify potential risks, and explain whether the proposed infrastructure change is safe and appropriate for human review.


### 5. Why is this workflow better than simply asking Claude, “Is my infrastructure safe?”

This workflow is better because it requires evidence-based analysis rather than relying solely on Claude's judgment. The Bash automation deterministically gathers Terraform plan results and performs predefined safety checks, while Claude interprets that evidence and identifies potential risks. The human then reviews the findings and remains responsible for approving any infrastructure changes.

This makes the process more reliable, auditable, and safer than simply asking Claude, “Is my infrastructure safe?” without providing verifiable evidence.


# Task 6 — Introduce a Controlled Difference and Detect It

## Goal

Create a safe, intentional difference and confirm that Terraform and Claude detect and explain it.

## Evidence

### Screenshot 11 — Controlled Difference

Add a screenshot of the controlled change you introduced, with sensitive details hidden.

![alt text](<controlled change you introduced, with sensitive details hidden.png>)


### Screenshot 12 — Detected Difference and Risk Assessment

Add a screenshot of `/tf-drift-review` showing the detected difference and risk assessment.

![alt text](<slash tf-drift-review showing the detected difference and risk assessment.png>)


### Screenshot 13 — Detected Drift Report

Add a screenshot of `drift-detected-report.txt` showing your full name and the `WARN` or `FAIL` result.

![alt text](<drift-detected-report.txt showing your full name and the WARN-1.png>)


## Questions

### 1. What change did you introduce?

I introduced a controlled configuration drift by manually changing the TestDrift tag on the AWS web-tier security group through the AWS Console.

Resource: book-review-web-sg (sg-06070d0de3855fb23)
Terraform expected value: TestDrift = "terraform-managed"
AWS Console value: TestDrift = "manual-change"


### 2. Was it true infrastructure drift or a Terraform configuration change?

It was true infrastructure drift, not a Terraform configuration change.

The change was made manually through the AWS Console by adding the TestDrift = "manual-change" tag to the EC2 instance’s security group. The Terraform configuration was left unchanged, creating a difference between the Terraform-defined desired state and the actual AWS infrastructure.

### 3. What Terraform plan evidence proves that a change is pending?

The plan shows TestDrift changing from manual-change to terraform-managed, followed by Plan: 0 to add, 1 to change, 0 to destroy. The -detailed-exitcode also returned 2, confirming that a change is pending.


### 4. Was the action an update, deletion, replacement, or security-rule change?

The action was an in-place update to the TestDrift tag on the web security group. No resources were deleted or replaced, and no security rules were modified.


### 5. What did Claude recommend?

Claude recommended that I review the detected drift and security-policy findings before applying any changes. It confirmed that drift exists, no destructive resource changes are planned, and two ingress rules allow unrestricted access from 0.0.0.0/0. Therefore, the changes should be reviewed by a human before any terraform apply is performed.


### 6. Why should you review the recommendation before taking action?

You should review Claude’s recommendation because AI analysis can identify risks and recommend actions, but the human remains responsible for deciding whether the proposed action is safe and appropriate. In this case, the plan includes a pending infrastructure change and two security-policy findings. Reviewing the evidence before applying ensures that no unintended or potentially harmful change is made to the AWS environment.


# Task 7 — Add a `PreToolUse` Hook to Block Unsafe Apply Attempts

## Goal

Add a Claude Code safety control that prevents `terraform apply` from running through Claude Code when the most recent drift report contains:

```text
Overall Status: FAIL
```

## Evidence

### Screenshot 14 — `PreToolUse` Safety Hook

Add a screenshot of `.claude/settings.json` showing the `PreToolUse` safety hook.


![alt text](<.claude slash settings.json showing the PreToolUse safety hook.png>)

### Screenshot 15 — Blocked Apply Attempt

Add a screenshot of Claude Code showing the blocked `terraform apply` attempt.


![alt text](<Claude Code showing the blocked terraform apply attempt.png>)


## Questions

### 1. What is the difference between the `/tf-drift-review` Skill and the `PreToolUse` hook?

/tf-drift-review is a workflow for investigation and reasoning. PreToolUse is a guardrail for controlling tool execution.


### 2. Which component performs analysis?

Claude performs the analysis and reasoning based on the evidence gathered by the Terraform drift-check script.


### 3. Which component enforces the safety gate?

PreToolUse acts as the safety gate by checking Claude’s proposed tool action before the action is executed. It can allow or block the action based on the defined safety rules.


### 4. Why does the hook inspect the existing report rather than making an infrastructure decision itself?

The hook inspects the existing report because its purpose is to enforce a deterministic safety gate, not to replace Claude’s reasoning or the human’s judgment. The report provides evidence that can be checked consistently before a high-impact action is allowed. This keeps the responsibilities separate: the script gathers evidence, Claude analyzes it, the hook enforces the safety gate, and the human makes the final decision.


### 5. Why is a deterministic guard useful for high-impact commands?

A deterministic guard is useful for high-impact commands because it applies the same predefined safety rules every time, without relying on AI judgment alone. It can block or require review before actions such as terraform apply or terraform destroy, reducing the risk of accidental or destructive changes. This provides a consistent safety boundary while leaving the actual infrastructure decision to the human.


# Task 8 — Resolve the Difference and Verify the Final State

## Goal

Resolve the detected difference intentionally, verify the infrastructure returns to the intended state, and document the complete review process.

## Evidence

### Screenshot 16 — Human-Reviewed Resolution

Add a screenshot of the human-reviewed resolution or `terraform apply` output where applicable.

![alt text](<human-reviewed resolution or terraform apply output where applicable.png>)


### Screenshot 17 — Final Healthy Review

Add a screenshot of the final `/tf-drift-review` showing `HEALTHY`.

![alt text](<final slash tf-drift-review showing HEALTHY.png>)


### Screenshot 18 — Saved Reports

Add a screenshot of `ls -lah reports` showing both:

- `drift-detected-report.txt`
- `resolved-report.txt`


![alt text](<ls -lah reports.png>)


### Screenshot 19 — Drift Review Summary

Add a screenshot of `drift-review-summary.md` showing all required sections and your full name.


![alt text](<drift-review-summary.md showing all required sections and your full name.png>)


## Terraform Drift Review Summary

### 1. Change Introduced

Explain the controlled change you introduced.

State whether it was:

- True infrastructure drift, or
- A Terraform configuration change

A manual tag (Key: TestDrift, Value: manual-change) was added directly to the
book-review-web-sg security group via the AWS Console, bypassing Terraform.
This was true infrastructure drift, not a Terraform configuration change — the
.tf files were never modified.


### 2. Evidence Collected

Describe the Terraform plan evidence and affected resource.


terraform plan -detailed-exitcode returned exit code 2, and the resulting plan
JSON showed one in-place update to module.security.aws_security_group.web,
removing the untracked TestDrift tag from tags and tags_all. No resources
were added or destroyed — Plan: 0 to add, 1 to change, 0 to destroy.



### 3. Risk Assessment

Explain the risk identified by the Bash check and Claude Code.


The Bash check and Claude Code both classified the pending change itself as
low-risk — a tag-only correction with no impact on security group rules,
ports, or ingress/egress CIDRs. However, the same policy scan flagged a
pre-existing SSH-from-anywhere rule (0.0.0.0/0 on port 22) on the web tier —
a real but unrelated, already-accepted exposure that caused the overall
report to show FAIL. Claude correctly distinguished this pre-existing
exposure from the pending change itself.


### 4. Human-Approved Action

Explain the action you reviewed and executed manually.

I reviewed terraform plan directly in the terminal, confirmed it only removed
the drift tag, and ran terraform apply manually (outside Claude Code and
outside the drift-check script) to reconcile the infrastructure back to
match the Terraform configuration. Apply completed with 0 added, 1 changed,
0 destroyed.



### 5. Verification

Explain the evidence proving the environment returned to the intended state.

A second terraform plan after the apply returned "No changes." A final
/tf-drift-review run confirmed Overall Status: HEALTHY, with all 3 checks
passing and no WARN or FAIL results.


### 6. Safety Decision

Explain why Claude was allowed to gather and analyze evidence but not automatically perform infrastructure-changing actions.

Claude was allowed to gather evidence and analyze it because that work is
low-risk and reversible — it only reads state via terraform plan and show.
Executing terraform apply is irreversible and can affect live infrastructure,
so that action was reserved for me. This was enforced by two independent
layers: CLAUDE.md's safety rules (which shaped Claude's behavior) and a
PreToolUse hook (a deterministic gate that blocks any terraform apply attempt
while the drift report shows Overall Status: FAIL, regardless of Claude's
own reasoning).


### 7. Agentic Loop Mapping

Explain how your workflow followed:

```text
Gather --> Analyze --> Human Act --> Verify
```

Gather: tf-drift-check.sh ran terraform plan -detailed-exitcode, converted
the plan to JSON, and checked for destructive actions and open ingress rules.

Analyze: the /tf-drift-review Skill read the generated report and JSON,
explained the drift in plain language, and distinguished the low-risk tag
change from the unrelated pre-existing SSH exposure.

Human Act: I reviewed terraform plan myself and ran terraform apply manually
after confirming the change was safe and expected.

Verify: a second /tf-drift-review run confirmed the environment returned to
HEALTHY, with no pending changes remaining.


## Questions

### 1. What action did you execute to resolve the difference?

I manually ran terraform apply to change the TestDrift tag from manual-change back to terraform-managed


### 2. Did you review `terraform plan` before taking action?


Yes. I reviewed the plan and confirmed it was only an in-place tag update with 0 added, 1 changed, and 0 destroyed.


### 3. What evidence proves the environment is now aligned?

A second terraform plan returned “No changes. Your infrastructure matches the configuration,” and the final drift review reported Overall Status: HEALTHY.


### 4. Why is a second drift review required after the fix?

It verifies that the intended change was successfully resolved and that no unexpected drift or security issues remain.


### 5. What could go wrong if an AI agent automatically applied every detected Terraform change?

It could apply an unsafe or destructive change, causing service disruption, data loss, security exposure, or unexpected infrastructure modifications.


### 6. In one sentence, explain the difference between asking an AI chatbot “Is my infrastructure okay?” and using this evidence-based Agentic AI workflow.

A chatbot gives an opinion based on the information it is given, while an evidence-based Agentic AI workflow collects actual Terraform evidence, analyzes the results, enforces safety controls, and keeps the human responsible for high-impact actions.

# LinkedIn Post — Mandatory

## Goal

Publish a LinkedIn post in your own words describing:

- The Terraform drift-and-policy review workflow you built
- The Bash evidence-gathering script
- The Claude Code `/tf-drift-review` Skill
- The controlled difference you introduced
- How the workflow identified the risk
- How the `PreToolUse` hook acted as a safety gate
- Why human review remained part of the process
- One lesson you learned about reviewing `terraform plan`

Include a screenshot of the detected change and a screenshot of the final `HEALTHY` review in your post.

Suggested tags:

```text
#DMIByPravinMishra #Terraform #AgenticAI #ClaudeCode #DevOps
```

## LinkedIn Evidence

### LinkedIn Post URL

https://www.linkedin.com/posts/felix-nwobodo-2a191856_dmibypravinmishra-terraform-agenticai-ugcPost-7513637998048026625-h90A/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE


### Published LinkedIn Post Screenshot — Mandatory

![alt text](<Linkedin post  for drift review with agentic AI.png>)

# Required Assignment Files

Confirm that the following files are included in your GitHub repository:

- `CLAUDE.md`
- `AI Assignment/tf-drift-check.sh`
- `.claude/skills/tf-drift-review/SKILL.md`
- `.claude/settings.json` containing the safety hook
- `reports/drift-detected-report.txt`
- `reports/resolved-report.txt`
- `drift-review-summary.md`

---

# Submission Instructions

- Complete Tasks 1–8 in sequence.
- Include Screenshots 1–19 exactly as specified.
- Answer every question under Tasks 1–8 in your own words.
- Complete all seven sections of the Terraform Drift Review Summary.
- Include the GitHub repository/folder URL containing the assignment files.
- Include your full name in the required reports and screenshots.
- Include the LinkedIn post URL and a screenshot of the published LinkedIn post.
- Do not expose access keys, passwords, tokens, account IDs, private keys, Terraform secrets, or other sensitive information.
- Review all screenshots carefully and hide or redact sensitive details where necessary.

---

# Completion Checklist

- [ ] Confirmed a clean Terraform baseline
- [ ] Created the required assignment workspace
- [ ] Created or updated `CLAUDE.md`
- [ ] Added project context and safety rules
- [ ] Created `tf-drift-check.sh`
- [ ] Added my full name to the report
- [ ] Validated the Bash script
- [ ] Made the script executable
- [ ] Used `terraform plan -detailed-exitcode`
- [ ] Used Terraform plan JSON
- [ ] Used `jq` to inspect destructive actions
- [ ] Used `jq` to inspect unsafe ingress
- [ ] Confirmed the baseline returns `HEALTHY`
- [ ] Created `/tf-drift-review`
- [ ] Restricted the Skill to appropriate tools
- [ ] Confirmed the Skill remains read-only
- [ ] Confirmed the Skill never runs `terraform apply`
- [ ] Confirmed the Skill never runs `terraform destroy`
- [ ] Introduced a controlled detectable difference
- [ ] Correctly identified whether it was true drift or a configuration change
- [ ] Saved `drift-detected-report.txt`
- [ ] Added the `PreToolUse` safety hook
- [ ] Verified the hook blocks `terraform apply` when the report is `FAIL`
- [ ] Reviewed the Terraform evidence before resolving the change
- [ ] Performed any infrastructure-changing action manually
- [ ] Ran the drift review again after resolution
- [ ] Confirmed the final status is `HEALTHY`
- [ ] Saved `resolved-report.txt`
- [ ] Completed `drift-review-summary.md`
- [ ] Mapped the workflow to `Gather --> Analyze --> Human Act --> Verify`
- [ ] Included all 19 numbered screenshots
- [ ] Answered all required questions
- [ ] Published the required LinkedIn post
- [ ] Added the LinkedIn post URL and screenshot
- [ ] Included the GitHub repository/folder URL
- [ ] Confirmed that no sensitive information is exposed

---

*This submission is part of the DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
