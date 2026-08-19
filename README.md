# skills

Agent skills. Each folder has a `SKILL.md` (frontmatter `name`/`description` plus instructions); sibling files are loaded on demand.

| Skill | Use it to |
| --- | --- |
| [`agent-feedback-init`](./agent-feedback-init) | add the `agent-feedback/` convention to a repo |
| [`agent-feedback-triage`](./agent-feedback-triage) | work a repo's `agent-feedback/items/` backlog one item per PR: reproduce, propose, fix with a guard, review locally, ship |

## Install

```sh
npx skills add DylanPiercey/skills
```

Or copy a folder into `.claude/skills/` (project) or `~/.claude/skills/` (global).

## The agent-feedback convention

Agents file out-of-scope findings as `agent-feedback/items/YYYY-MM-DD-<slug>.md` with `type`/`impact`/`effort`/`site` frontmatter and a mandatory `Check:` repro line. One file per item: parallel branches never conflict and a rebase cannot resurrect a deleted item. Resolution is a fix or a maintainer's won't-fix comment at the code site; the item file is deleted either way. Full rules live in the README the init skill installs.
