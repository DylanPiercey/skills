---
name: agent-feedback-scan
description: Use when asked to fill the agent-feedback backlog ("scan for agent feedback", "run a feedback wave"). Runs agents through realistic workloads, verifies findings, files what survives.
disable-model-invocation: true
---

# agent-feedback-scan

Run agents (lanes) through realistic workloads, verify what they hit, file the survivors. Lanes never write to `agent-feedback/`. Companions: `agent-feedback-init` installs the convention, `agent-feedback-triage` works the backlog.

Before starting read `agent-feedback/README.md` (item format, `## Repo notes`); missing means run `agent-feedback-init` first. Read the repo's `AGENTS.md`/`CLAUDE.md`; its git rules override the steps below.

## The loop

1. **Aim.** List `site:` and `type` across `agent-feedback/items/`. Target surfaces not listed.
2. **Write workloads.** One per surface targeted in step 1. Each is a task a user of the project would actually do, stated as requirements ("must keep working after a restart"), not as APIs or idioms to use. At least one lane may not read implementation source. Chain where cheap: a later lane extends, migrates or operates an earlier lane's output. Roles to draw from:
   - first use: start from the project's own getting-started path
   - docs only: no source, including installed packages
   - inheriting: another lane's output plus change requests, without its notes
   - migrating: from a previous version or a competing tool, incrementally
   - operating: the built or published artifact, not the dev loop
   - edges: unusual inputs, precedence, limits, as requirements
   - constrained: one restriction the project claims to support
   - diagnostics: quality of errors on a surface no item covers
   - contributing: clone, build, test, land a small change by the contributing guide
3. **Run.** One subagent per lane, parallel if the host allows. Each gets the brief below, its workload, a scratch directory outside the repo, and a port range.
4. **Collect.** Concatenate `observations.jsonl` files. Read each `commands.log` for non-zero exits and retry loops the lane did not log.
5. **Cluster and dedupe.** Merge across lanes. Route each to the code that owns the fix; another project's code is reported in chat, not filed. `grep -ril '<path or symbol>' agent-feedback/items`: a match is a duplicate unless it adds information. "Undocumented" where `searched` shows the docs cover it is a discoverability defect instead.
6. **Verify.** Read the code site; an intent comment means deliberate, drop it. Reproduce with the Repo notes' commands; if it does not fail, drop it. Verify per area. Leave the repo clean.
7. **File, review and stop.** Write survivors in the README's format with the probe as `Check:`. One commit named for the findings. Show `git show --stat HEAD`, the items, each refutation and its probe, and which lanes were mostly duplicates. Wait for "ship".
8. **Ship.** `git push -u origin HEAD`, PR body says what the items cover in a few sentences, no process. Link it and stop.

## The lane brief

> You are a developer experienced with comparable tools, new to this project, on a deadline. Report friction plainly.
>
> Work only in `<scratch>`, use ports `<range>`, kill servers by port owner. Never modify the repo, write to `agent-feedback/`, or run a git write command. Check what the test command does before running it.
>
> Log every command and exit code to `commands.log`. Append to `observations.jsonl` when friction happens, not at the end: non-zero exits, errors you had to interpret, lookups that did not answer, wrong guesses, silent no-ops, and anything better than expected.
>
> `{"symptom","expected","actual","repro","searched":[],"owner","confidence"}`
>
> `searched` is the exact terms tried, required when claiming something is undocumented. `owner` is your guess at the file at fault. `confidence` is whether this is the project's fault or yours; `low` is fine. Never invent an observation.
>
> Finish with the deliverable, the three costliest problems, every server dead, and what you could not finish.

## Judgement

- Corroboration across lanes orders verification; it is not evidence.
- Never use git history to decide a finding is resolved. `items/` and code-site comments are the only record.
