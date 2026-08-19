---
name: agent-feedback-init
description: Use when a repo should start capturing agent feedback ("set up agent-feedback", "add an agent feedback backlog", "where should agents file out-of-scope findings"). Installs agent-feedback/README.md and the AGENTS.md pointer.
---

# agent-feedback-init

Install the agent-feedback convention: `agent-feedback/README.md` (rules, format, repo notes) and `agent-feedback/items/` (one file per finding). Companion skill: `agent-feedback-triage` works the backlog.

This skill is run once per repo, so it does not need to be installed anywhere. `agent-feedback-triage` does: it is used on every item.

## Steps

1. Copy `README.template.md` from this skill to `agent-feedback/README.md`.
2. Fill `## Repo notes`. Derive from the repo's AGENTS.md/CLAUDE.md, package scripts, and test layout: how to reproduce a claim, where guard tests go, snapshot/update commands, pre-ship checks, known gotchas. Ask the user for anything you cannot derive, and propose notes you think belong before writing them. Keep it dense; it is read on every triage.
3. Add to the repo's `AGENTS.md` (or `CLAUDE.md`):

   ```md
   ## Agent feedback

   Anything actionable but out of scope for the current task (suspected bug, cleanup, perf or size win, tooling friction, confusing code) must be filed in [`agent-feedback/`](agent-feedback/README.md) before finishing. Never drop it silently. Never fix it inside an unrelated diff.
   ```
4. Commit. Report the paths.
5. Tell the user to install the triage skill in this repo so an agent can reach for it when working the backlog:

   ```sh
   npx skills add DylanPiercey/skills -s agent-feedback-triage
   ```

   Suggest `-g` instead if they triage backlogs in several repos.

## Prose rules for the files you write

Dense. Short imperative sentences. Cut any clause a competent reader infers. Never use an em dash; delete the aside or split the sentence.
