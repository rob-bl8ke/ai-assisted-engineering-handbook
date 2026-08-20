# Playbook: Sync Skills With Upstream Repositories

## Purpose

Use this playbook to keep a skill current with one or more upstream source repositories on an unattended recurring schedule, with a pull request review gate before anything lands.

This is useful when a skill encodes knowledge that actually lives in another repository, such as configuration keys, annotations, version numbers, setup steps, API behavior, or package conventions.

This is a concrete application of the [Scheduled Agentic Work](../patterns/scheduled-agentic-work.md) pattern: the sync skill carries the domain-specific update procedure, the scheduled task defines cadence and invocation, and the executor can be swapped when the permission model changes.

The repository-to-skill mapping, baseline SHA tracking, state file, time gate, and PR gate are harness-neutral. The `.claude/settings.local.json`, `scheduled-tasks` MCP, `/schedule`, and Claude Desktop runtime details below are Claude-specific adapter guidance.

## Problem

A skill can drift out of date after the upstream repository changes. The fix is a small recurring job that:

1. Checks upstream repositories for commits since the last reviewed SHA.
2. Has an agent update genuinely affected skills.
3. Records where it left off so the next run only inspects new commits.
4. Opens a pull request for review instead of landing changes directly.
5. Runs on a schedule so a human does not need to remember to trigger it.

## When to Use

- A skill depends on behavior, config, commands, or conventions owned by another repository.
- The upstream repository changes often enough that manual checks are unreliable.
- The affected skill changes can be reviewed through normal PR workflow.
- You can give the agent a narrow read/write and command allowlist.

## When Not to Use

- The upstream source is unstable and requires human interpretation every time.
- You cannot restrict the agent to known local clone paths and known skill directories.
- The sync needs to merge automatically without human review.
- A normal dependency update tool, CI check, or documentation generator already solves the drift.

## Step 1: Map Repositories to Skills

Write down which upstream repositories each skill depends on. Treat this as a many-to-many mapping: one skill can depend on several repositories, and one repository can feed several skills.

Hypothetical example:

| Dependent skill | Depends on upstream repositories |
|---|---|
| `skills/widget-usage/SKILL.md` | `widget-core` |
| `skills/widget-usage-e2e-tests/SKILL.md` | `widget-core` |
| `skills/widget-and-gadget-integration/SKILL.md` | `widget-core`, `gadget-core` |

This mapping should become data in the sync state file, not hardcoded behavior inside the skill logic.

## Step 2: Prepare Local Upstream Clones

The sync check compares Git SHAs, so each upstream repository needs a local clone with a working `origin` remote.

```bash
git clone https://github.com/your-org/widget-core ~/code/widget-core
git clone https://github.com/your-org/gadget-core ~/code/gadget-core
```

Confirm the tracked branch exists before assuming `main` or `develop`:

```bash
git -C ~/code/widget-core ls-remote --heads origin
git -C ~/code/gadget-core ls-remote --heads origin
```

Record the current HEAD SHA for each branch you will track:

```bash
git -C ~/code/widget-core rev-parse HEAD
git -C ~/code/gadget-core rev-parse HEAD
```

These SHAs seed the baseline. They mean: everything up to this commit is already accounted for; only inspect what comes after.

## Step 3: Create the Sync Skill

Create a sync skill directory, such as `skills/sync-widget-skills/`, with a runbook and a state file.

Example `skills/sync-widget-skills/SKILL.md`:

```markdown
---
name: sync-widget-skills
description: "Checks widget-core and gadget-core for upstream commits since the last recorded sync, and updates dependent skills when something relevant changed. Runs on a scheduled task, or on demand."
argument-hint: "[force] - pass 'force' to bypass the time gate and run the real check now"
---

# Sync Widget Skills

## Fixed Scope

- Read-only on the exact `local_clone` paths listed in `state/sync-state.json`.
- Never clone a new repository.
- Never read an unlisted path.
- Writes are limited to the skill directories named in the mapping table.
- Every write goes through a branch and pull request.
- Never commit or push to `main` directly.
- Never merge, force-push, or delete a branch the skill did not create.

## Procedure

1. Read `state/sync-state.json`.
2. Unless invoked with `force`, skip if less than the configured window has elapsed since `last_full_check_at`.
3. For each repo entry, fetch and compare the latest SHA to `last_synced_sha`.
4. For repos with new commits, review the log and diff for consumer-relevant changes such as version bumps, config changes, setup changes, deprecations, or changed usage patterns.
5. Ignore internal churn, test-only changes, and implementation details that do not affect skill users.
6. For each affected skill, edit only if genuinely impacted.
7. Update state by advancing `last_synced_sha`, `last_synced_at`, and `last_full_check_at` as appropriate.
8. Branch, commit, push, and open a PR describing the SHA ranges and changes. Never merge it yourself.
```

Example `skills/sync-widget-skills/state/sync-state.json`:

```json
{
  "last_full_check_at": "2026-08-19T16:54:49Z",
  "repos": {
    "widget-core": {
      "url": "https://github.com/your-org/widget-core",
      "local_clone": "~/code/widget-core",
      "branch": "main",
      "last_synced_sha": "<HEAD SHA from step 2>",
      "last_synced_at": "2026-08-19T16:54:49Z",
      "affects": [
        "skills/widget-usage/SKILL.md",
        "skills/widget-usage-e2e-tests/SKILL.md",
        "skills/widget-and-gadget-integration/SKILL.md"
      ]
    },
    "gadget-core": {
      "url": "https://github.com/your-org/gadget-core",
      "local_clone": "~/code/gadget-core",
      "branch": "main",
      "last_synced_sha": "<HEAD SHA from step 2>",
      "last_synced_at": "2026-08-19T16:54:49Z",
      "affects": [
        "skills/widget-and-gadget-integration/SKILL.md"
      ]
    }
  }
}
```

Keep configuration and snapshot state in one file. The mapping is user-editable, and the agent updates the snapshot after each run. This avoids a separate manifest drifting from the state.

Use a time gate inside the skill instead of relying on a literal weekly cron. The scheduled task can fire daily, while the skill decides whether enough time has elapsed to do real work.

## Step 4: Lock Down the Blast Radius

Because the job runs unattended, scope must be capped structurally.

Required boundaries:

- Repo allowlist: read only the exact `local_clone` paths in the state file.
- Write allowlist: edit only the mapped dependent skill paths and the sync skill's own state.
- PR-only workflow: every write goes through branch, commit, push, and PR creation, including timestamp-only state updates.
- No direct `main` writes: never commit or push to `main` directly.
- No privileged Git operations: never merge, force-push, reset hard, or delete branches the skill did not create.

Claude-specific example `.claude/settings.local.json` permission allowlist:

```json
{
  "permissions": {
    "allow": [
      "Bash(git -C ~/code/widget-core fetch origin main)",
      "Bash(git -C ~/code/widget-core rev-parse origin/main)",
      "Bash(git -C ~/code/widget-core log --oneline *)",
      "Bash(git -C ~/code/widget-core diff --stat *)",
      "Bash(git -C ~/code/gadget-core fetch origin main)",
      "Bash(git -C ~/code/gadget-core rev-parse origin/main)",
      "Bash(git -C ~/code/gadget-core log --oneline *)",
      "Bash(git -C ~/code/gadget-core diff --stat *)",
      "Bash(git checkout -b sync/*)",
      "Bash(git push origin sync/*)",
      "Bash(gh pr create *)",
      "Edit(skills/widget-usage/**)",
      "Edit(skills/widget-usage-e2e-tests/**)",
      "Edit(skills/widget-and-gadget-integration/**)",
      "Edit(skills/sync-widget-skills/**)",
      "Write(skills/sync-widget-skills/**)"
    ]
  }
}
```

This Claude settings file is usually local, machine-specific, and gitignored. Verify with:

```bash
git check-ignore -v .claude/settings.local.json
```

The allowlist is the security boundary, not the wording of the skill prompt. If the skill tries something outside this list, it should hit a permission wall.

## Step 5: Configure the Schedule

This section is Claude-specific. For other executors, use the same sync skill, state file, time gate, and PR policy, but replace the `scheduled-tasks` MCP invocation with that executor's scheduling mechanism.

Use a scheduled-task mechanism to call the sync skill. In Claude Desktop environments, this can be done through the `scheduled-tasks` MCP server, with tools such as `mcp__scheduled-tasks__create_scheduled_task`, or a `/schedule` command backed by that server.

Create a task with:

- `taskId`: a short kebab-case id, such as `sync-widget-skills`.
- `cronExpression`: a daily cron string, such as `35 11 * * *`, rather than a literal weekly cron.
- `notifyOnCompletion`: `false`, so no-op days do not create notification noise.
- `prompt`: a fully self-contained instruction because the task has no memory of the setup conversation.

Use a daily cron plus a skill-level time gate rather than a literal weekly cron. If the app or machine is not running at one weekly firing, the check can be missed for a full week. A daily fire-and-gate setup catches up the next day the app is open, while the skill's `last_full_check_at` gate prevents daily real work.

Example scheduled task shape:

```json
{
  "tool": "mcp__scheduled-tasks__create_scheduled_task",
  "taskId": "sync-widget-skills",
  "description": "Daily-triggered, weekly-gated check of widget-core/gadget-core for upstream changes",
  "cronExpression": "35 11 * * *",
  "notifyOnCompletion": false,
  "prompt": "Working directory: ~/code/my-skills. Run the sync-widget-skills skill exactly as documented in skills/sync-widget-skills/SKILL.md: check the gate, and if due, fetch widget-core/gadget-core, diff against the last recorded SHA, update only genuinely impacted skills, advance the state file, and open a PR. Never push or merge to main directly. If anything cannot be done safely, stop and explain why."
}
```

## Step 6: Understand the Claude Desktop Runtime Model

This section applies to Claude Desktop scheduled tasks. Do not generalize it to OS cron, GitHub Actions, Copilot Automations, OpenCode, or Claude cloud Routines without checking that executor's runtime model.

Do not assume this is an OS-level cron job unless you configured OS cron or launchd yourself.

In Claude Desktop scheduled-task setups:

- `crontab -l` and `launchctl list` may show nothing for the scheduled task.
- The schedule is held and evaluated by the Claude Desktop app process.
- When a scheduled time arrives, the app can spawn a `claude` CLI child process to execute the task prompt.
- The schedule only fires while Claude Desktop is running.
- If the app is closed when a task is due, the documented behavior is to run on next launch.

This is why the daily cron plus skill-level gate matters. The goal is not exact-minute execution; it is reliable-enough recurring review when the desktop app is active.

If you need guaranteed execution independent of the desktop app, use a real OS-level `cron` or `launchd` job that invokes the CLI headlessly.

## Step 7: Operate the Claude Scheduled Task

This section is Claude-specific. Other executors should provide equivalent operations for listing, manually running, pausing, resuming, and deleting the scheduled invocation.

Common operations:

- List tasks with `mcp__scheduled-tasks__list_scheduled_tasks`; the listing should show each task's cron expression, enabled state, `nextRunAt`, `lastRunAt`, and prompt `path`.
- Read the task prompt from the on-disk `path` returned by the listing. Claude scheduled-task prompts are stored outside the repository, commonly as `{taskId}/SKILL.md` under `~/.claude/scheduled-tasks/`.
- Run the skill manually with a force argument, such as `/sync-widget-skills force`.
- Pause the task with `mcp__scheduled-tasks__update_scheduled_task` and `enabled: false`.
- Resume by setting `enabled: true`.
- Delete with `mcp__scheduled-tasks__delete_scheduled_task`. Deletion stops the task; keep or inspect any remaining prompt file as local reference rather than treating it as handbook source of truth.

Scheduled tasks are callers. The sync skill remains the primary entry point and should be runnable manually for testing.

## Checklist

1. Write down the repository-to-skill mapping.
2. Confirm every upstream repository has a local, fetchable clone.
3. Confirm the real branch name with `git ls-remote --heads origin`.
4. Record baseline SHAs.
5. Create the sync skill and seed `state/sync-state.json`.
6. Add a skill-level time gate.
7. If using Claude locally, add narrowly scoped `.claude/settings.local.json` permissions for fetch, diff, branch, push, PR creation, and affected skill edits.
8. Create a daily scheduled task that invokes the skill; let the skill's gate decide whether a given day is a real check.
9. If using Claude Desktop scheduled tasks, remember they only fire while the desktop app is running, or on next launch if supported.
10. Test manually with `force` before waiting for the schedule.
11. Verify the result opens a PR and never lands changes directly.

## Related

- [Skills Overview](../skills/README.md) - Skill lifecycle and maintenance guidance.
- [Skill Maturity Criteria](../skills/skill-maturity-criteria.md) - When a playbook should become an executable skill.
- [Scheduled Agentic Work](../patterns/scheduled-agentic-work.md) - Harness-neutral pattern for recurring repository maintenance.
- [Configure Claude Code Hooks](configure-claude-code-hooks.md) - Related Claude Code guardrail configuration.
- [Unified Agent Workspace](../patterns/unified-agent-workspace.md) - Control-plane placement for shared skills, scripts, and standards.

## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Scheduled upstream skill sync | User-provided tutorial | Practitioner example | Describes recurring skill synchronization from upstream repositories using local clones, state files, PR gates, scoped permissions, and scheduled tasks | N/A |
| Scheduled task runtime model | User-provided system observation | Practitioner observation | Notes Claude Desktop scheduled tasks are app-managed rather than OS cron, motivating daily fire-and-gate scheduling | N/A |
