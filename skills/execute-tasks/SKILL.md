---
name: execute-tasks
description: Executes one or more previously created task files under .plans/<TICKET>/tasks, using the plan and docs folders as context, then marks tasks Done, propagates findings to pending tasks, and files durable findings into the project's wiki when it has one. Use when asked to execute a task, run tasks for a ticket, implement a task file, or continue work on a plan.
allowed-tools: Read, Edit, Write, Grep, Glob, Bash, AskUserQuestion
---

# Execute Tasks

You are responsible for executing one or more tasks based on instructions created previously (see the **create-plan** and **create-tasks** skills).

## Workflow

1. **Get the ticket number.** Tasks live in `.plans/<TICKET>` (e.g. `.plans/TASK-1234`). The user must provide the ticket number; if they don't, use the default prefix `TASK`. If the user already provided it in the prompt, don't ask again.

2. **Validate the folder exists.** If the ticket folder can't be found, or the user provides no usable ticket number, stop and advise the user to create a plan first using the **create-plan** skill.

3. **Gather context.** Read, in this order:
   - `.plans/<TICKET>/plan` — base information, typically `<TICKET>-implementation-plan.md` and `<TICKET>-requirements.md`.
   - `.plans/<TICKET>/tasks` — the task (or tasks) requested by the user.
   - `.plans/<TICKET>/docs` — documentation generated as the outcome of previously finished tasks, for broader context (may not exist).
   - `wiki/index.md` and the wiki's schema file, **only if a `wiki/` folder exists** at the repository root — the project's knowledge base and the conventions it is maintained under. The schema is the agent-instruction file inside `wiki/`: look for `wiki/AGENTS.md` first, then any other agent-instruction file the project uses (`wiki/CLAUDE.md`, `wiki/GEMINI.md`, `wiki/.cursorrules`, and similar). Some are one-line pointers to the canonical one — follow the pointer. Most projects have no wiki at all; skip silently when absent.

4. **Identify which task(s) to execute.** Use what the user asked for (a specific number, a range, or "the next pending one"). If ambiguous, ask.

5. **Ask before executing.** Before starting, ask as many questions as needed to ensure you and the user have a mutual understanding of the actions to be performed in the task. Ask one question at a time, preferring multiple choice.

6. **Execute the task.** Follow the task's `Steps`, honoring the `Updates from Previous Tasks` section and the plan's constraints. Set the task `## Status` to `In Progress` while working on it.

7. **Generate files when needed.** Any file you need to produce (reports, audit files, analyses, investigations, etc.) goes in `.plans/<TICKET>/docs`, creating the folder if needed. Name them:

   `<NN>-<TICKET>-name-of-the-file.md` (e.g. `02-TASK-1234-security-audit.md`)

   where `<NN>` is the same number as the corresponding task.

8. **Close out the task.**
   - Verify every acceptance criterion and requirement was fulfilled; check off the boxes in `## Acceptance Criteria`.
   - Set `## Status` to `Done`, followed by a brief summary of why the task is considered done.
   - Review the remaining pending tasks and, for any that are affected by what you just executed, append the relevant information to their `## Updates from Previous Tasks` section — decisions made, files touched, deviations from the plan, and gotchas.

9. **Update the knowledge base, if the project has one.** Check whether a `wiki/` folder exists at the repository root. If it doesn't, skip this step entirely — most projects have no wiki and this is a no-op for them.

   When `wiki/` does exist:

   - **Follow the project's own schema.** If the wiki has a schema file (located as described in step 3), it defines that wiki's page types, frontmatter, linking and logging conventions — obey it over anything below. The rest of this step is the default for a wiki with no schema file.
   - **Apply the filter. Most tasks write nothing, and that is the expected outcome.** Write only when the task produced one of:
     - external evidence — data or rules obtained from outside the repository
     - a decision whose rationale the code cannot state, including the alternatives rejected
     - a spec added or changed
     - a contradiction discovered, whether between two sources or between a documented claim and the code
   - **Write nothing** for bug fixes, refactors, renames, new tests, styling or dependency bumps. If the answer to a question is "read the code", it belongs in the code. A wiki that updates on every task is a worse copy of `git log`.
   - **Move, don't copy.** When something from `.plans/<TICKET>/docs` qualifies, relocate it into the wiki rather than duplicating it. `.plans` is a working area and is frequently gitignored, so anything left there alone is lost.
   - **Keep navigation current.** If the wiki has an index, add or update the entry. If it has a log, append one entry recording what was filed and the commit the codebase was at.

10. **Report.** Summarize what was executed, which files changed, any docs generated, which subsequent tasks were updated, and what — if anything — was filed into the wiki.

## Status handling

- Valid values: `Pending`, `In Progress`, `Done`, `Blocked` (include a short reason next to `Blocked`).
- If a task cannot be completed, mark it `Blocked` with the reason instead of `Done`, and tell the user what's needed to unblock it.

## Guardrails

- Never mark a task `Done` when acceptance criteria are unmet or unverified.
- Don't expand scope beyond the task and its plan; if new work is discovered, report it and suggest a new task rather than silently doing it.
- Execute tasks in their numbered order unless the user explicitly asks otherwise.
- Never write to the wiki something the code, the seed data or git history already states — a page that restates the repository rots the moment the repository changes.
- Validate changes using the project's existing build/lint/test tooling before closing a task.
- Never skip step 5 — no execution before mutual understanding is confirmed.
