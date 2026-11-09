# Assignment 5 — AI-Assisted Azure DevOps Dual-Pipeline Failure Triage

Part of the DevOps Micro Internship (DMI) — Agentic AI Track

---

## Student Information

**Full Name:** [Felix Emeka Nwobodo]

**GitHub Repository or Fork URL:** [https://github.com/Felimek28/epicbook-dual-pipeline.git]

**Public LinkedIn Post URL:** [https://www.linkedin.com/posts/felix-nwobodo-2a191856_azuredevops-aws-devops-ugcPost-7525519291409076224-E9-Q/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE]

---

## Purpose

In this assignment, I configured an AI-assisted, read-only failure-triage workflow for the EpicBook Infrastructure and Application Pipelines. The workflow uses Bash to gather Azure DevOps pipeline evidence and Claude Code to analyze the evidence and recommend a recovery action while keeping all changes under human control.

---

# Task 0 — Verify Tools, Authentication, and Pipeline Details

## Goal

Verify the required tools, Azure DevOps authentication, organization and project details, and numeric pipeline IDs.

No screenshot is required for this task.

---

# Task 1 — Capture the Healthy Baseline and Prepare the Supplied Files

## Goal

Confirm that both EpicBook pipelines are healthy and place the supplied assignment files in the correct repository locations.

## Evidence

### Screenshot 1 — Healthy Baseline for Both Pipelines

Terminal output showing the latest completed Infrastructure and Application Pipeline runs with successful results.

![alt text](<Terminal output showing the latest completed Infrastructure and Application Pipeline runs with successful results.png>)

## Notes

### 1. What proves that both pipelines were healthy before the drill?

[The Azure DevOps pipeline run records confirm that both pipelines completed successfully on the main branch:
Infrastructure Pipeline (ID 4): Run ID 34, build number 20261108.3, status completed, result succeeded. This confirms that Terraform successfully validated, planned, and applied the infrastructure changes.
Application Pipeline (ID 5): Run ID 35, build number 20261108.1, status completed, result succeeded. This confirms that the application pipeline successfully completed its deployment process, including the Ansible deployment.
]

### 2. Why is a healthy baseline necessary before introducing a controlled failure?

[A healthy baseline provides evidence that the system was functioning correctly before the failure was introduced. It allows us to distinguish between pre-existing problems and issues deliberately introduced during the drill. It also provides a reference point for evaluating troubleshooting, recovery, and the effectiveness of corrective actions. This makes it possible to demonstrate that the system can recover and return to a successful operating state after a controlled failure]

---

# Task 2 — Configure and Review the Supplied CLAUDE.md

## Goal

Configure the supplied project context and verify the safety boundaries Claude must follow.

## Evidence

### Screenshot 2 — CLAUDE.md Context and Safety Rules

`CLAUDE.md` open in the editor with the Project Overview, Incident Workflow, Safety Rules, and Output Rules visible.

![alt text](<CLAUDE.md open in the editor with the Project Overview, Incident Workflow, Safety Rules, and Output Rules visible..png>)

## Notes

### 1. Why does Claude need project-specific operational context?

[So it understands the roles of the infrastructure and application pipelines and knows which evidence to analyze.]

### 2. Which rules keep the human responsible for the recovery action?

[Human review provides control over production changes and prevents an automated agent from making potentially harmful modifications.]

### 3. Which rules protect pipeline credentials and application secrets?

[The Safety Rules prohibit accessing or exposing secrets, modifying Azure resources, changing service connections or permissions, and triggering or modifying pipeline runs]



# Task 3 — Configure and Validate the Supplied Pipeline Triage Script

## Goal

Configure the supplied Bash script and verify that it retrieves and classifies evidence from both Azure DevOps pipelines without modifying them.

## Evidence

### Screenshot 3 — Pipeline Triage Script Configuration

Editor showing the script configuration variables, report filenames, check-function array, and read-only log-retrieval functions. Ensure that no token is visible.


![alt text](<Pipeline Triage Script Configuration.png>)

---

### Screenshot 4 — Script Validation

Terminal showing successful Bash syntax validation and executable file permission.

![alt text](<Terminal showing successful Bash syntax validation and executable file permission.png>)

## Notes

### 1. Why are pipeline metadata and step console logs handled separately?

[Pipeline metadata and step console logs serve different purposes. Metadata provides information such as the pipeline run ID, branch, status, result, and completion time. Step console logs provide detailed execution output that helps identify what caused a failure. Handling them separately allows the script to determine the pipeline's state first and retrieve detailed logs when needed for failure analysis.]

### 2. How does the script obtain the actual console logs?

[The script obtains an Entra access token using the Azure CLI az account get-access-token command. It then makes authenticated, read-only HTTP GET requests to the Azure DevOps Build Logs REST API. First, it retrieves the list of log IDs for a build. Next, it downloads each individual log using its log ID. The retrieved content is sanitized before being written to the appropriate report log file]

### 3. How does the check-function array control the classification loop?

[The categories array stores the required failure categories and their associated regular-expression patterns. The run_category_checks() function loops through each array entry, extracts the category name and pattern, and searches the sanitized log file for matching evidence. Every matching category is recorded in the report, allowing the script to identify multiple failure types in one run]

### 4. What prevents a failed but unmatched run from being reported as healthy?

[The script checks the pipeline's actual run result independently of the failure-category matching. If the run has failed, it remains a pipeline failure even when none of the known patterns match. The classification function returns an unmatched result, allowing the pipeline-processing logic to report an Unclassified Pipeline Failure instead of treating the run as healthy]

### 5. Why are different exit codes useful to another automation tool?

[Different exit codes allow another automation tool to identify the outcome of the triage process without interpreting the entire report. Exit code 0 means both pipelines are healthy, 1 indicates a warning or incomplete result, 2 indicates a pipeline failure, and 3 indicates a script configuration, authentication, API, or log-retrieval error. This makes it easier for other tools to decide whether to continue, alert an engineer, or request investigation]

---

# Task 4 — Run and Understand the Healthy-State Report

## Goal

Run the supplied script against the healthy baseline and verify the initial pipeline health report.

## Evidence

### Screenshot 5 — Healthy Pipeline Report

Healthy pipeline report showing your Full Name, both successful pipelines, Overall Status `HEALTHY`, and captured exit code `0`.

![alt text](<Healthy pipeline report showing your Full Name, both successful pipelines, Overall Status HEALTHY, and captured exit code 0.png>)

## Notes

### 1. What evidence proves that both pipelines are healthy?

[The triage report confirms that both pipelines are healthy because:

The Infrastructure Pipeline (ID: 4, Run ID: 34) completed successfully on the main branch, with status completed and result succeeded.

The Application Pipeline (ID: 5, Run ID: 35) completed successfully on the main branch, with status completed and result succeeded.

Both pipelines have [PASS] results.

The summary reports PASS: 2, WARN: 0, FAIL: 0, and ERROR: 0.

The overall status is HEALTHY, and the script returns exit code 0.

These results provide evidence that both pipelines' latest completed runs succeeded.]

### 2. Why must the baseline exit code be verified before the incident drill?

[The baseline exit code must be verified to establish that the triage script is functioning correctly and that both pipelines are healthy before introducing a simulated failure. In this run, exit code 0, together with Overall Status: HEALTHY, confirms a successful baseline. This provides a reliable comparison point for the incident drill and helps distinguish an actual pipeline failure from a script configuration, dependency, authentication, or API error]

---

# Task 5 — Configure and Test the Supplied /pipeline-triage Skill

## Goal

Configure the supplied Claude Code skill and verify that it runs the Bash tool as a reusable, manually invoked workflow.

## Evidence

### Screenshot 6 — Pipeline-Triage Skill Definition

`SKILL.md` showing the frontmatter, manual-invocation setting, narrowly scoped tools, safety rules, and required output structure.

![alt text](<SKILL.md showing the frontmatter, manual-invocation setting, narrowly scoped tools, safety rules, and required output structure.png>)

---

### Screenshot 7 — Healthy Skill Result

Healthy `/pipeline-triage` result showing that both pipelines are healthy and no fix is required.

![alt text](<Healthy Skill Result from claude.png>).

## Notes

### 1. Why is `disable-model-invocation: true` appropriate for this skill?

[disable-model-invocation: true ensures that Claude cannot automatically invoke the skill on its own. It requires the user to manually start /pipeline-triage, giving the engineer control over when pipeline checks are performed and preventing unexpected execution]

### 2. Why should the skill avoid broad Bash approval?

[Broad Bash approval could allow unrestricted shell commands, including commands that modify project files, change infrastructure, expose secrets, or trigger pipeline operations. Restricting approval to the exact read-only triage command reduces the risk of unintended changes and keeps the workflow within its intended scope.]

### 3. What work is performed by Bash, and what work is performed by Claude?

[Bash executes pipeline-triage.sh. It retrieves pipeline metadata and console logs from Azure DevOps, sanitizes log content, checks for known failure patterns, and generates the health report and sanitized log files.

Claude reads the generated report and sanitized logs, interprets the results, identifies warnings or failures, explains evidence-supported causes, and recommends a recovery action for the engineer to review. Claude does not apply the fix.]

### 4. Why are permission rules required in addition to written safety instructions?

[Written safety instructions tell Claude what it should and should not do, but they are not a technical security boundary. Permission rules provide an additional enforcement layer by restricting dangerous commands and unauthorized tools. Combining both helps prevent accidental file modifications, secret exposure, infrastructure changes, and pipeline mutations.]

---

# Task 6 — Introduce a Safe Failure in the Application Pipeline

## Goal

Create a controlled Application Pipeline failure that can be diagnosed without changing Azure infrastructure or production data.

## Evidence

### Screenshot 8 — Controlled Application Pipeline Failure

Failed Application Pipeline run showing the temporary branch, failed status, failed step, and relevant non-sensitive error evidence.

![alt text](<Failed Application Pipeline run showing the temporary branch, failed status, failed step, and relevant non-sensitive error evidence.png>)

## Notes

### 1. What exact failure did you introduce?

[I introduced a deliberate ESLint failure by creating a temporary JavaScript file named triage-drill-failure.js containing an unused variable

The existing ESLint no-unused-vars rule detected that the variable was declared and assigned a value but never used. Consequently, the npm test command failed during the Application Pipeline's BuildAndTest stage.]

### 2. Which category should detect it?

[Test Failure.

The failure occurred during the application's test/validation stage when npm test executed the ESLint checks and returned an error. It should be classified as a Test Failure rather than an infrastructure or deployment failure.
]

### 3. Why is the failure safe and easily reversible?

[The failure is safe because it affects only a temporary JavaScript file in my application repository. It does not modify Terraform configuration, Ansible playbooks, credentials, AWS resources, databases, or production infrastructure.

It is easily reversible by deleting triage-drill-failure.js and removing the temporary drill branch after completing the exercise.
]

### 4. How did you prevent the deliberate failure from reaching `main` or changing the deployed application?

[I created a separate Git branch named drill/pipeline-failure and committed the intentional failure only to that branch.

I pushed the branch to GitHub and manually queued Application Pipeline ID 5 against it. The pipeline's trigger configuration includes only the main branch, and pull-request validation is disabled.

The failure occurs in the BuildAndTest stage, before deployment. The Deploy stage depends on BuildAndTest succeeding, so the failed validation prevents deployment from proceeding. I did not merge the temporary branch into main.]

---

# Task 7 — Diagnose and Save the Incident Evidence

## Goal

Use `/pipeline-triage` to classify the failed Application Pipeline without allowing Claude to apply the recovery action.

## Evidence

### Screenshot 9 — Failed-State Diagnosis and Incident Report

`/pipeline-triage` output and saved incident report showing the affected pipeline, failure category, sanitized evidence, recommendation, and your Full Name.

![alt text](<output of slash pipeline-triage on claude-1.png>)

![alt text](<saved incident report showing the affected pipeline, failure category, sanitized evidence.png>)

## Notes

### 1. Which failure category was identified?

Unclassified Pipeline Failure.

The Application Pipeline failure was confirmed, but the triage script could not identify the specific category because the Azure DevOps Build Logs API returned HTTP 403 when it attempted to retrieve the logs.

### 2. What exact evidence supported the diagnosis?

[The following evidence was reported:

Application Pipeline ID: 5.

Run ID: 36.

Branch: refs/heads/drill/pipeline-failure.

Status: completed.

Result: failed.

Infrastructure Pipeline ID 4, run 34, completed successfully on main.

The Build Logs API returned HTTP 403, preventing retrieval of the application console logs.

The report recorded PASS 1, WARN 0, FAIL 1 and ERROR 1.

This confirmed that the application pipeline failed, but the exact failure category could not be established from the logs available to Claude.
]

### 3. Did Claude apply the fix or rerun the pipeline? Why is that important?

[No. Claude did not apply any fix, retry the pipeline, or modify the infrastructure. It only inspected the pipeline results, analyzed the available evidence, and recommended that I review the Azure DevOps Build Read and Logs Read permissions before inspecting the failed run manually.

This is important because it preserves human control, prevents unauthorized changes to infrastructure or application deployments, and ensures that corrective actions are reviewed and approved by the]

### 4. Which part represents Gather, and which part represents Analyze?

[Gather: Claude executed pipeline-triage.sh, retrieved pipeline metadata, checked the latest run statuses, attempted to retrieve build logs, and collected the available evidence into the triage report.

Analyze: Claude compared the pipeline results, confirmed that Application Pipeline run 36 failed while Infrastructure Pipeline run 34 succeeded, identified the HTTP 403 log-access error, classified the failure as Unclassified Pipeline Failure, and recommended the next diagnostic steps without assuming an unverified root cause.]

---

# Task 8 — Apply the Human-Reviewed Fix and Verify Recovery

## Goal

Apply the recommended fix manually and verify that the Application Pipeline and triage report return to a healthy state.

## Evidence

### Screenshot 10 — Corrected Application Pipeline Run

Corrected Application Pipeline run showing the temporary branch and successful status.

![alt text](<Corrected Application Pipeline run showing the temporary branch and successful status.png>)

---

### Screenshot 11 — Recovery Triage Result

Recovery `/pipeline-triage` output showing Overall Status `HEALTHY`, exit code `0`, your Full Name, and both saved report filenames.

![alt text](<Recovery pipeline-triage output showing Overall Status HEALTHY.png>)

## Notes

### 1. What exact fix did you apply?

[I manually removed the intentionally introduced file triage-drill-failure.js from the temporary branch drill/pipeline-failure. I committed the deletion using commit message Fix: remove intentional pipeline triage failure (commit 2b1eb95) and pushed the change to GitHub]

### 2. Did the fix match Claude’s recommendation? Explain briefly.

[Yes. Claude recommended manually fixing the identified application pipeline failure rather than automatically changing the code. Removing the deliberately introduced unused variable file addressed the ESLint no-unused-vars error identified in the pipeline logs]

### 3. What evidence proves that the pipeline recovered?

[The corrected Application Pipeline was manually rerun against drill/pipeline-failure and completed successfully. The deliberate failure file had been removed, and the pipeline no longer failed at the application test stage.]

### 4. Why is a second triage run required after the pipeline becomes green?

[A second triage run verifies the latest pipeline results rather than relying on the previous failure report. It checks whether the application and infrastructure pipelines are healthy, updates the health report, and provides evidence that the recovery was successful. The expected result is HEALTHY with exit code 0]

### 5. What risk would be created if Claude could automatically edit, push, approve, and rerun the pipeline?

[Giving Claude all those permissions would create excessive automation and security risks. It could introduce unintended code changes, push unreviewed changes, bypass human approval, expose credentials, or trigger deployments that affect production infrastructure. Human review and approval provide accountability and prevent potentially harmful automated actions]

---

# LinkedIn Post — Mandatory

## LinkedIn Post URL

[https://www.linkedin.com/posts/felix-nwobodo-2a191856_azuredevops-aws-devops-ugcPost-7525519291409076224-E9-Q/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE]

## Evidence

### Screenshot 12 — Published LinkedIn Post

Published LinkedIn post showing its text and at least one image or link.

![alt text](<Linkedin post for AI assisted epicbook cicd.png>)

# Required Repository Files

Confirm that the following files are available in your repository:

* [ ] `CLAUDE.md`
* [ ] `pipeline-triage.sh`
* [ ] `.claude/skills/pipeline-triage/SKILL.md`
* [ ] `reports/incident-failure-report.txt`
* [ ] `reports/recovery-report.txt`

---

# Submission Instructions

* Complete all tasks in sequence.
* Include all 12 required screenshots.
* Answer every Notes question in your own words.
* Include your GitHub repository or fork URL.
* Include your public LinkedIn post URL.
* Ensure your Full Name appears in the required reports.
* Do not include raw logs containing sensitive information.
* Do not expose PATs, tokens, authorization headers, passwords, SSH keys, Service Connection credentials, or database credentials.

---

# Completion Checklist

* [ ] Both Azure DevOps pipelines were healthy before the drill.
* [ ] The supplied files were copied to the correct repository locations.
* [ ] Only the required student-specific placeholders were updated.
* [ ] `CLAUDE.md` contains the required context and safety rules.
* [ ] `pipeline-triage.sh` passed Bash syntax validation.
* [ ] The script has executable permission.
* [ ] The script uses read-only Azure DevOps operations.
* [ ] The script retrieves pipeline metadata and console logs.
* [ ] No token or password is stored in the script.
* [ ] The healthy baseline reported `HEALTHY` with exit code `0`.
* [ ] `/pipeline-triage` was invoked manually.
* [ ] The skill does not have broad Bash approval.
* [ ] The controlled failure affected only the Application Pipeline.
* [ ] The failure occurred before deployment changes were applied.
* [ ] The deliberate failure was not merged into `main`.
* [ ] The failed-state report was saved before applying the fix.
* [ ] Claude diagnosed the failure but did not apply the fix.
* [ ] The fix was reviewed and applied manually.
* [ ] The corrected Application Pipeline completed successfully.
* [ ] The recovery triage reported `HEALTHY` with exit code `0`.
* [ ] `incident-failure-report.txt` exists.
* [ ] `recovery-report.txt` exists.
* [ ] All Notes questions have been answered.
* [ ] All 12 screenshots have been added.
* [ ] The GitHub repository or fork URL has been included.
* [ ] The LinkedIn post is public.
* [ ] The LinkedIn post URL has been included.
* [ ] No sensitive information is exposed.

---

# Final Submission

**Full Name:** [Felix Emeka Nwobodo]

**GitHub Repository or Fork URL:** [https://github.com/Felimek28/epicbook-dual-pipeline.git]

**LinkedIn Post URL:** [https://www.linkedin.com/posts/felix-nwobodo-2a191856_azuredevops-aws-devops-ugcPost-7525519291409076224-E9-Q/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE]

---

*This submission is part of the DevOps Micro Internship (DMI) — Agentic AI Track.*
