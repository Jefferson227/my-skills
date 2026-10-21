# my-skills

A small collection of [Agent Skills](https://skills.sh) for a plan → tasks → execute workflow.

| Skill | What it does |
| --- | --- |
| [`create-plan`](skills/create-plan/SKILL.md) | Turns a ticket, document, or prompt into an implementation plan and requirements summary under `.plans/<TICKET>/plan`. |
| [`create-issue`](skills/create-issue/SKILL.md) | Publishes the plan as an issue on GitHub (or GitLab, Jira, Azure DevOps) titled `<TICKET>: <title>`, and records the issue in the plan. |
| [`create-tasks`](skills/create-tasks/SKILL.md) | Splits that plan into individual task files under `.plans/<TICKET>/tasks`. |
| [`execute-tasks`](skills/execute-tasks/SKILL.md) | Executes those task files, marks them Done, and propagates findings to pending tasks. |

They are meant to be used in order, but each works on its own.

## Install

### As a Claude Code plugin

```
/plugin marketplace add Jefferson227/my-skills
/plugin install my-skills@jefferson227
```

Plugin skills are namespaced: `/my-skills:create-plan`, `/my-skills:create-issue`, `/my-skills:create-tasks`, `/my-skills:execute-tasks`. Asking in plain language still triggers them.

Update with `/plugin marketplace update jefferson227`, remove with `/plugin uninstall my-skills@jefferson227`.

To try local changes without installing:

```bash
claude --plugin-dir .
```

### With the `skills` CLI

```bash
npx skills add Jefferson227/my-skills
```

Install a single skill:

```bash
npx skills add Jefferson227/my-skills --skill create-plan
```

Install everything globally for Claude Code, without prompts:

```bash
npx skills add Jefferson227/my-skills --skill '*' -g -a claude-code -y
```

List what's in the repo without installing:

```bash
npx skills add Jefferson227/my-skills --list
```

## Usage

Once installed, ask your agent for the work and it will pick the skill up from the description:

```
create a plan for PROJ-123
create an issue for PROJ-123
create the tasks for PROJ-123
execute task 1 of PROJ-123
```

## Update / remove (`skills` CLI)

```bash
npx skills update create-plan create-issue create-tasks execute-tasks
npx skills remove create-plan create-issue create-tasks execute-tasks
```

## License

MIT
