---
name: create-plan
description: Creates a detailed implementation plan and requirements summary from user-provided inputs (Jira tickets, documents, or prompts), asking clarifying questions until requirements are mutually understood, then writes the plan and requirements files under .plans/<TICKET>/plan. Use when asked to create an implementation plan, plan a task/ticket, or draft a plan document.
allowed-tools: Read, Grep, Glob, Bash, Write, AskUserQuestion
---

# Create Plan

You are responsible for creating an implementation plan based on inputs from the user.

## Workflow

1. **Source of truth.** The plan must be based on initial document(s) — most likely from Jira or another tracking tool — or from a prompt describing the definition of what needs to be done.

2. **Get the ticket number.** Ask the user for the Jira ticket number (e.g. `TASK-1234`). The user may have already provided it in the prompt — in that case, don't ask again. This is optional: if the user doesn't provide one and declines to when asked, use the default prefix `TASK-*` (i.e., use `TASK` as the placeholder throughout).

3. **Gather and clarify requirements.** Read the inputs from the user (file(s) and/or prompt text). Ask the user as many clarifying questions as needed — one at a time — until you and the user have a mutual, unambiguous understanding of what needs to be changed. Don't proceed to planning while material ambiguity remains.

4. **Draft the implementation plan.** Based on the clarified inputs, create a detailed implementation plan containing:
   - A summary of the changes to be applied
   - The implementation actions to be taken (concrete, ordered steps)
   - A checklist of deliverables

5. **Write the implementation plan file.** Save it as `<TICKET>-implementation-plan.md` in the folder `.plans/<TICKET>/plan`, where `<TICKET>` is the Jira ticket number provided by the user, or the default prefix `TASK` if none was provided.

6. **Write the requirements file.** Create `<TICKET>-requirements.md` containing a summary of the requirements derived from the user's input. Save it in the same folder `.plans/<TICKET>/plan`, and link it from the implementation plan file (e.g. a "Requirements" reference/link near the top of `<TICKET>-implementation-plan.md` pointing to `<TICKET>-requirements.md`).

## Implementation plan format

```
# <TICKET> Implementation Plan

Requirements: [<TICKET>-requirements.md](./<TICKET>-requirements.md)

## Summary
<2-4 sentences describing the change and its goal>

## Implementation Actions
1. <Concrete step>
2. <Concrete step>
...

## Deliverables Checklist
- [ ] <Deliverable 1>
- [ ] <Deliverable 2>
...
```

## Requirements file format

```
# <TICKET> Requirements

Source: <Jira ticket / document(s) / prompt referenced by the user>

## Summary
<Concise summary of what's being requested>

## Requirements
- <Requirement 1>
- <Requirement 2>
...

## Open Questions / Assumptions (optional)
- <Any assumptions made or questions resolved during clarification>
```

## Guardrails

- Never skip the clarification step — do not fabricate requirements or invent scope not confirmed by the user.
- Ask one question at a time, and prefer multiple-choice questions when the options are predictable.
- If no ticket number is given, consistently use `TASK` as `<TICKET>` everywhere (folder name and file names).
- Create the `.plans/<TICKET>/plan` directory if it doesn't already exist.
- Keep both files concise and actionable — avoid vague or generic filler text.
- Do not implement any code changes as part of this skill; only produce the planning documents.
