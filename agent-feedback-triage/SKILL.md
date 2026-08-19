---
name: agent-feedback-triage
description: Use when asked to triage or work the agent-feedback backlog, pick the next agent-feedback item, or when the user says "next" after a merge. Works agent-feedback/items/ one item per PR (reproduce, propose, fix with a guard, delete the item, review locally, ship).
---

# agent-feedback-triage

Items are written by other agents. Many are wrong, stale, or against project goals. Reproduce before believing.

Before starting read `agent-feedback/README.md`, especially `## Repo notes` (repro commands, test conventions, pre-ship checks). Follow the workspace's branch conventions if any (CLAUDE.md/AGENTS.md at the workspace root); the git steps below assume none.

## The loop

1. **Sync.** `git fetch && git reset --hard origin/<base>`. Each PR is one commit on the latest base.
2. **Pick by value.** Highest `impact` tier first; within a tier, lowest `effort`. List candidates:
   ```sh
   for t in high med low; do grep -l "^impact: $t" agent-feedback/items/*.md; done
   ```
   First list items whose `site:` path is gone; they are near-free deletions, offer them before picking:
   ```sh
   for f in agent-feedback/items/*.md; do p=$(sed -n 's/^site: \([^ ›]*\).*/\1/p' "$f"); [ -e "$p" ] || echo "$f"; done
   ```
   Skip only what the user rejected in this session. Never consult git history to decide whether an item is resolved: presence in `items/` means unresolved.
3. **Reproduce (red) before anything else.** Run the item's `Check:` line. Missing or `TODO`: reproduce from the body; if you cannot, that item is the first deletion candidate. Use the Repo notes' repro commands. No repro: say so, delete the item in its own commit only if the user agrees, then pick another.
4. **Propose and stop.** Quote the item file verbatim so the user can judge the premise. Then: fix sketch, guard-test plan, expected cost (whatever the Repo notes call out), whether a changeset or release note is warranted. When more than one direction is credible, present each with its trade-off and a recommendation. Wait for "go". Follow redirects ("just a comment", "won't fix", "do the whole thing") without re-litigating.
5. **Implement.** Guard with a test that is red without the fix and green with it. Prove red by reverting only the fix files, running, restoring. Fold tests into existing suites; no new one-off files.
6. **Prove green.** Re-run the check, the touched suites, then the repo's full pre-ship checks from Repo notes. Report any cost delta before committing.
7. **Delete the item file in the same commit.** Delete any item that duplicates it too. Fix stale references in sibling items. Never file new items or delete unrelated ones during triage; report side findings and unrelated duplicates in chat.
8. **Local review and stop.** Single commit. Show `git show --stat HEAD`, the key hunks, and any cost delta. Wait for "ship". Confirm no stray files.
9. **Ship.** `git push -f -u origin HEAD`, then a PR with a brief what/why body. No verification logs, no file-by-file detail. Link the PR and stop; the user merges.
10. On "merged" / "next": step 1.

## Resolutions

Every item ends as exactly one of:

- **Fix** with a red/green guard.
- **Won't-fix**, only on the user's explicit call. Add a comment (two lines max) at the code site: what the behavior is and why it is deliberate. Never history ("removed in #NNN"). Then delete the item. The comment is mandatory; deleting alone lets agents re-file.
- **Diagnostic**: the misuse stays possible but is reported (build error, lint rule, dev-only warning). Must not change production output.

## Judgement

- The user maintains the project. When an item's premise smells wrong, offer won't-fix-with-comment instead of defending the item.
- For risky or disputed approaches, offer an adversarial second opinion via any available spawn-* skill.
- Prefer fixes that cost nothing at runtime (tooling, compile-time) when two directions are equal.
