---
name: create-issue
description: Creates an issue on GitHub (or another platform such as GitLab, Jira or Azure DevOps) from a previously created implementation plan under .plans/<TICKET>/plan, titled "<TICKET>: <ticket title>", then records the issue number in the plan with a note to close it once all tasks are done. Use when asked to create an issue, open a GitHub issue, or publish a ticket's plan as an issue.
allowed-tools: Read, Grep, Glob, Bash, Edit, AskUserQuestion
---

# Create Issue

You are responsible for creating an issue on GitHub — or any other platform the user works with — based on an implementation plan previously created (see the **create-plan** skill).

## Workflow

1. **Get the ticket number.** The issue is based on the plan stored in `.plans/<TICKET>/plan` (e.g. `.plans/TASK-1234/plan`). The ticket number must be provided by the user; if they already provided it in the prompt, don't ask again. If none is provided, ask for it. If no plan folder exists for the ticket, stop and advise the user to create a plan first using the **create-plan** skill.

2. **Detect the platform.** Default to GitHub. Infer the platform from the git remote (`git remote get-url origin`) or from what the user said: `github.com` → GitHub, `gitlab` → GitLab, `dev.azure.com` / `visualstudio.com` → Azure DevOps, Jira when the user mentions it. If it's unclear, ask the user.

3. **Check the CLI tool.** The user must have the platform's CLI installed and authenticated:

   | Platform | CLI | Check |
   |---|---|---|
   | GitHub | `gh` | `gh auth status` |
   | GitLab | `glab` | `glab auth status` |
   | Azure DevOps | `az` (with `azure-devops` extension) | `az account show` |
   | Jira | `jira` (jira-cli) or `acli` | `jira me` / `acli jira auth status` |

   If the CLI is missing or not authenticated, and no other CLI tool that can access the platform is available, stop. Advise the user to skip the issue creation and use the **create-tasks** skill to create the tasks locally instead. Don't try to create the issue through any other route (e.g. raw API calls with tokens).

4. **Load the context.** If `.plans/<TICKET>/TICKET_CONTEXT.md` exists, read it first to get the context faster.

5. **Read the plan.** Read every file in `.plans/<TICKET>/plan` — typically `<TICKET>-implementation-plan.md` and `<TICKET>-requirements.md`. The issue must reflect the plan's actual scope; don't invent anything that isn't there.

6. **Draft the issue.**
   - **Title:** `<TICKET>: <ticket title>` (e.g. `TASK-1234: Add order selector to checkout`). Take the ticket title from the plan or requirements.
   - **Body:** use the format below.

7. **Check for duplicates.** Search the platform for an existing issue whose title starts with `<TICKET>:` (e.g. `gh issue list --search "<TICKET> in:title" --state all`). If one exists, show it to the user and ask whether to reuse it or create a new one.

8. **Confirm and create.** Show the user the title and body, and create the issue once they confirm. For GitHub, write the body to a temporary file and run:

   ```
   gh issue create --title "<TICKET>: <ticket title>" --body-file <body-file>
   ```

   Use the equivalent command for other platforms (`glab issue create`, `az boards work-item create`, `jira issue create`, …).

9. **Report.** Confirm to the user the issue number and URL on the platform.

10. **Update the plan.** Append the section below to `.plans/<TICKET>/plan/<TICKET>-implementation-plan.md`. If `TICKET_CONTEXT.md` exists, add the issue number and URL to it too.

## Issue body format

```
## Summary
<2-4 sentences: what the ticket asks for and why>

## Requirements
- <Requirement from the requirements file>
...

## Implementation Plan
1. <High-level implementation action from the plan>
...

## Acceptance Criteria
- [ ] <Deliverable / verifiable outcome from the plan>
...

---
Plan: `.plans/<TICKET>/plan/<TICKET>-implementation-plan.md`
```

## Plan note format

```
## Issue

- Platform: <GitHub | GitLab | Jira | Azure DevOps | …>
- Issue: #<number> — <url>

> **Note:** This issue must be closed when all tasks for <TICKET> are `Done`, or earlier upon request by the user.
```

## Guardrails

- Never create the issue without showing the draft to the user and getting confirmation — it is published to an external service.
- Never invent scope that isn't in the plan.
- If the plan folder for the ticket doesn't exist or is empty, do not guess: tell the user to run the **create-plan** skill first.
- Don't close the issue as part of this skill; only add the note to the plan.
- Don't implement any code changes as part of this skill.
- If the plan already has an `## Issue` section, tell the user and ask before creating another issue.
