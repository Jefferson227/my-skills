---
name: create-tasks
description: Breaks a previously created implementation plan into individual task files under .plans/<TICKET>/tasks, one markdown file per task, each with a status and an updates-from-previous-tasks section. Use when asked to create tasks from a plan, split a plan into tasks, or generate task files for a ticket.
allowed-tools: Read, Grep, Glob, Bash, Write, AskUserQuestion
---

# Create Tasks

You are responsible for creating tasks based on an implementation plan previously created (see the `create-plan` skill).

## Workflow

1. **Get the ticket number.** The tasks are derived from the plan stored in `.plans/<TICKET>/plan` (e.g. `.plans/TASK-1234/plan`). The user may have already provided the ticket number in the prompt — in that case, don't ask again. If none is provided, use the default prefix `TASK`. If no plan folder exists for the ticket, stop and advise the user to create a plan first using the **create-plan** skill.

2. **Read the plan.** Read every file in `.plans/<TICKET>/plan` — typically `<TICKET>-implementation-plan.md` and `<TICKET>-requirements.md` — so the tasks reflect the actual scope, ordering, and deliverables.

3. **Break the plan into tasks.** Derive a set of ordered, self-contained tasks from the implementation actions and deliverables checklist. Each task should be independently executable and verifiable, and should not duplicate work from another task.

4. **Write one markdown file per task.** Save them in `.plans/<TICKET>/tasks`, creating the folder if it doesn't exist. Name files using a zero-padded sequence number reflecting execution order:

   `01-<TICKET>-name-of-the-task.md` (e.g. `01-TASK-1234-add-order-selector.md`)

   Use lowercase, hyphen-separated task names.

5. **Confirm.** Report the list of created task files to the user.

## Task file format

```
# <NN>. <Task title>

Ticket: <TICKET>
Plan: [<TICKET>-implementation-plan.md](../plan/<TICKET>-implementation-plan.md)

## Status
Pending

## Objective
<1-3 sentences describing what this task accomplishes>

## Steps
1. <Concrete step>
2. <Concrete step>
...

## Acceptance Criteria
- [ ] <Verifiable outcome 1>
- [ ] <Verifiable outcome 2>
...

## Updates from Previous Tasks
<Empty at creation time. Relevant information discovered while executing previous
tasks is appended here — decisions made, files touched, deviations from the plan,
gotchas that affect this task.>
```

## Status handling

- Every task file starts with a `## Status` section containing `Pending`.
- The status is changed to `Done` either by the user or by the model once the task is considered finished.
- Valid values: `Pending`, `In Progress`, `Done`, `Blocked` (include a short reason next to `Blocked`).

## Guardrails

- Never invent scope that isn't in the plan — tasks must trace back to the implementation actions or deliverables.
- If the plan folder for the ticket doesn't exist or is empty, do not guess: tell the user to run the **create-plan** skill first.
- Keep the numbering contiguous (`01`, `02`, `03`, …) and matching the intended execution order.
- Do not implement any code changes as part of this skill; only produce the task files.
- Don't overwrite existing task files without telling the user; if the folder already has tasks, ask whether to regenerate or add to them.
