# Assignment 5 — AI-Assisted Sprint Health Report via Jira MCP

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will connect Claude Code to your Jira board through an MCP server, the same way you connected it to GitHub in Week 2, and build a read-only `/sprint-health` skill. The skill reads your current sprint through Jira's API and reports sprint velocity, stories at risk of missing the sprint, and items missing an estimate — but it must never create, edit, comment on, or transition a single ticket itself. You will prove that boundary holds by making a real change on the board yourself and confirming the skill only ever reports, never acts.

---

# Task 1 — Create a Jira API Token

## Goal

Generate an API token from your Atlassian account that the MCP server will use to authenticate with your Jira site. Do not screenshot the token value itself.

### Evidence

#### Screenshot 1 — Jira API token creation confirmation page showing the token name, with the token value not visible


![alt text](<Jira API token creation confirmation page showing the token name, with the token value not visible.png>)


### Notes You Must Write (Very Important):

Why does the MCP server need your site URL and account email in addition to the token?

The MCP server needs all three pieces of information to establish the correct user, credentials, and Jira destination. The email and token authenticate the account, while the site URL determines which Jira instance the MCP server should interact with.
In simple terms:
Email → Who are you? Identifies the Atlassian account.
API token → Are you authorized? Proves that the account is allowed to make the request.
Site URL → Where should the request go? Identifies the specific Jira instance.


# Task 2 — Create .mcp.json at the Project Root

## Goal

Create or update `.mcp.json` at your project root with a Jira MCP server block, following the same shape as the GitHub MCP server you configured in Week 2.

### Evidence

#### Screenshot 2 — `.mcp.json` open in VS Code showing the Jira server configuration

![alt text](<mcp.json open in VS Code showing the Jira server configuration.png>)


### Notes You Must Write (Very Important):

Compare this jira block to the github block from Week 2 Assignment 5. The GitHub server ran via npx (a Node.js package); this one runs via uvx (a Python package) — what stays exactly the same shape despite that difference, and why doesn't Claude Code care which language a given MCP server is written in?

What stays exactly the same shape despite that difference
The runtime changes (npx vs. uvx), but the MCP contract remains the same. Claude Code interacts with the standardized MCP interface rather than directly interacting with the server's programming language.

Why doesn't Claude Code care about the programming language?
Because Claude Code doesn't need to understand the server's implementation language. It communicates with the running server through the Model Context Protocol (MCP), which provides a standardized interface.


# Task 3 — Add Your Credentials to settings.local.json

## Goal

Add your Jira site URL, account email, and API token to `.claude/settings.local.json`, and confirm that file is listed in `.gitignore` so it is never committed.

### Evidence

#### Screenshot 3 — `settings.local.json` open in VS Code showing the `env` section, with the actual token value blurred or covered


![alt text](<settings.local.json open in VS Code showing the env section, with the actual token value blurred or covered.png>)


### Notes You Must Write (Very Important):

Why must JIRA_API_TOKEN live in settings.local.json and never in .mcp.json?

JIRA_API_TOKEN should be kept in settings.local.json rather than hard-coded in .mcp.json because .mcp.json is configuration, while the API token is a secret.

.mcp.json should define how Claude Code starts and connects to the Jira MCP server.
settings.local.json can hold machine-specific secrets that should remain local.
The actual token should never be committed to Git or pushed to GitHub.


# Task 4 — Verify the Connection with /mcp

## Goal

Restart Claude Code and confirm the Jira MCP server shows as connected.

### Evidence

#### Screenshot 4 — `/mcp` output showing `jira: connected`

![alt text](<mcp output showing jira connected.png>)

# Task 5 — Run a Live Query to Prove Real Board Data

## Goal

Ask Claude to list the issues in your current active sprint through the Jira MCP connection, and confirm the result matches what you see on your live board in the browser.

### Evidence

#### Screenshot 5 — Claude's response showing the live sprint issue list retrieved via Jira MCP

![alt text](<Claude's response showing the live sprint issue list retrieved via Jira MCP.png>)

### Notes You Must Write (Very Important):

How did you confirm this was real board data and not something Claude guessed?

I checked from Jira board and what I have on the board is what Claude retrieved for me. No fabrication


# Task 6 — Build the /sprint-health Skill

## Goal

Create a `/sprint-health` skill restricted to read-only Jira tools plus `Read`, with no issue-mutating tools and no `Write`. Run it and confirm it produces a report covering sprint velocity, at-risk stories, and items missing an estimate.

### Evidence

#### Screenshot 6 — `SKILL.md` frontmatter showing `allowed-tools` limited to read-only Jira tools plus `Read`, with `disable-model-invocation: true`


![alt text](<SKILL md frontmatter showing allowed tools limited to read.png>)


#### Screenshot 7 — `/sprint-health` output showing the full triage report against your real sprint


![alt text](<sprint-health output showing the full triage report against your real sprint before changes.png>)



### Notes You Must Write (Very Important):

1. Which Jira MCP tools does this skill's allowed-tools list include, and which mutating tools (create issue, update issue, transition issue, add comment) does it deliberately exclude?

Jira MCP tools included in allowed-tools
Allowed tool	           Purpose
mcp__jira__jira_search	    Search Jira issues
mcp__jira__jira_get_issue	Retrieve details for a specific issue
mcp__jira__jira_get_sprint	Retrieve sprint information
mcp__jira__jira_get_board	Retrieve Scrum board information

Mutating Jira tools deliberately excluded
jira_create_issue — create an issue
jira_update_issue — edit/update an issue
jira_transition_issue — change an issue's workflow status
jira_add_comment — add a comment to an issue


2. Why does a Scrum Master need this restriction more than almost any other role in this course?

A Scrum Master needs this restriction because the role is to facilitate and guide the team, not let AI make decisions or modify the board automatically.

# Task 7 — Prove the Skill Never Mutates the Board

## Goal

Manually update one ticket on your board in the browser (for example, move a story to "Done" or add a missing estimate), then run `/sprint-health` again and confirm the new report reflects your change — proving the skill only ever reads live state and never wrote to the board itself.

### Evidence

#### Screenshot 8 — Second `/sprint-health` run showing the report now reflects your manual board change


![alt text](<sprint health on claude after change.png>)


### Notes You Must Write (Very Important):

Map this assignment to Gather → Analyze → Human Act → Verify from Week 3 Assignment 6. Which step did you perform manually in the browser, and why must that step stay human?

Step	What happens in this assignment
Gather	Jira MCP tools gather the active sprint, issues, statuses, assignees, story points, and update timestamps.
Analyze	/sprint-health calculates velocity, identifies at-risk stories, and finds missing estimates/acceptance criteria.
Human Act	The Scrum Master manually decides and takes action in the Jira browser—for example, updating an issue, transitioning it, or adding a comment.
Verify	The Scrum Master checks Jira afterward to confirm the intended change was correctly applied.

The human act was done by me by manually updating the jira board on the browser from To do  to Done.It must stay human because the AI should recommend and identify problems, not make Scrum decisions on behalf of the team



# Submission Instructions

Complete all tasks in sequence.

Your submission must include:
- All 8 required screenshots
- All the required notes

---

# Completion Checklist

- [ ] Task 1: Jira API token created, value never screenshotted (Screenshot 1)
- [ ] Task 2: `.mcp.json` has the Jira server block (Screenshot 2)
- [ ] Task 3: Credentials stored in `settings.local.json`, token blurred, file gitignored (Screenshot 3)
- [ ] Task 4: `/mcp` shows the Jira server connected (Screenshot 4)
- [ ] Task 5: Live query returned real sprint data, verified against the browser (Screenshot 5)
- [ ] Task 6: `/sprint-health` skill created with correct read-only `allowed-tools`, and produced a full report (Screenshots 6–7)
- [ ] Task 7: A manual board change was reflected in a second `/sprint-health` run (Screenshot 8)
- [ ] Skill never created, edited, transitioned, or commented on any issue
- [ ] Reflection answered (Notes)
- [ ] No API token value exposed

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

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
