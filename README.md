# skills

Agent skills. Each folder has a `SKILL.md` (frontmatter `name`/`description` plus instructions); sibling files are loaded on demand.

| Skill | Use it to | Install as |
| --- | --- | --- |
| [`agent-feedback-init`](./agent-feedback-init) | add the `agent-feedback/` convention to a repo | nothing, run it once |
| [`agent-feedback-scan`](./agent-feedback-scan) | fill the backlog: run lanes through realistic workloads, verify every finding adversarially, file what survives | project or global skill |
| [`agent-feedback-triage`](./agent-feedback-triage) | work a repo's `agent-feedback/items/` backlog one item per PR: reproduce, propose, fix with a guard, review locally, ship | project or global skill |

## Setup

### `agent-feedback-init`: run once, do not install

Setting up a repo happens one time, so there is no reason to keep the skill on disk. Run it without installing:

```sh
npx skills use DylanPiercey/skills@agent-feedback-init
```

That prints a prompt to paste into your agent; `-a claude` starts Claude Code with it instead.

### `agent-feedback-scan` and `agent-feedback-triage`: install them

You fill the backlog and work it item by item over time, so both should be on disk. Triage is picked up by the agent on its own; scan launches several agents, so it runs only when you invoke it. Install them into the repo whose backlog you keep:

```sh
npx skills add DylanPiercey/skills -s agent-feedback-scan -s agent-feedback-triage
```

Add `-g` instead to install them globally if you keep backlogs across several repos. Copying the folders into `.claude/skills/` (project) or `~/.claude/skills/` (global) works too.

## The agent-feedback convention

Agents file out-of-scope findings as `agent-feedback/items/YYYY-MM-DD-<slug>.md` with `type`/`impact`/`effort`/`site` frontmatter and a mandatory `Check:` repro line. One file per item: parallel branches never conflict and a rebase cannot resurrect a deleted item. Resolution is a fix or a maintainer's won't-fix comment at the code site; the item file is deleted either way. Full rules live in the README the init skill installs.
