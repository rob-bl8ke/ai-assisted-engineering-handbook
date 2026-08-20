# Scheduled Agentic Work

## Overview

Scheduled agentic work is a pattern for running recurring repository maintenance through AI agents without turning each tool into a separate automation design.

Separate the recurring job into three layers:

```text
Domain knowledge
  skills / AGENTS.md / review guidance
        ↓
Task definition
  schedule, scope, output policy, safety rules
        ↓
Executor
  Claude, Copilot, OpenCode, GitHub Agentic Workflows, or another agent runtime
```

The task definition should say what maintenance job to run and what outputs are allowed. The skill or repository guidance should contain the expertise. The executor should be replaceable where practical.

## Problem

Repositories need recurring care: security reviews, resilience reviews, architecture drift checks, dependency reviews, documentation drift checks, and skill synchronization. Humans forget these tasks, but naive automation creates other problems:

- Each agent harness grows its own prompt, schedule, permissions, and state model.
- Scheduled jobs repeatedly scan entire repositories and waste tokens.
- Agents may be granted write access before the workflow is trustworthy.
- Findings can be duplicated every run because previous state is not tracked.
- Tool-specific setup hides the underlying maintenance policy.

## Solution

Define scheduled maintenance as a reusable repository layer rather than a collection of cron prompts.

```text
Schedule
   ↓
checkout repository
   ↓
invoke agent
   ↓
load skill or repository guidance
   ↓
analyse incrementally
   ↓
produce findings
   ├─ no significant findings -> do nothing
   └─ findings -> report, issue, or PR according to policy
```

A repository can expose scheduled work like this:

```text
Repository
├── .agents/
│   ├── skills/
│   │   ├── security-review/
│   │   └── resilience-review/
│   └── prompts/
│       ├── scheduled-security-review.md
│       └── scheduled-resilience-review.md
└── .github/
    └── workflows/
```

The scheduled prompt should stay small. For example, it should say `Use the resilience-review skill to analyse changes since the previous review`, not embed the full resilience checklist.

## Design Rules

- Keep domain expertise in skills, `AGENTS.md`, standards, and playbooks.
- Keep schedule, trigger, scope, state, and output policy in the task definition.
- Keep executor glue in adapters, GitHub Actions, Routines, Automations, or agentic workflow tooling.
- Prefer incremental analysis from the last reviewed commit over whole-repository scans.
- Record enough state to avoid rediscovering or re-reporting unchanged findings.
- Start read-only and graduate permissions only after the workflow proves useful.
- Route meaningful changes through issues or PRs rather than direct unattended commits.

## Incremental Review

Scheduled jobs should usually compare against prior state:

```text
main
  ↓
commits since last audit
  ↓
git diff
  ↓
changed dependency graph
  ↓
targeted agent analysis
```

If repository graph tooling is available, use it to expand from changed files to affected callers, dependencies, contracts, services, or runbooks. If graph tooling is not available, fall back to search and repository exploration.

```text
git diff
   ↓
files actually changed
   ├─ graph available -> dependency traversal
   └─ no graph -> grep/search
   ↓
focused review
```

This applies the [Context Management](context-management.md) pattern to unattended work: give the agent enough connected context to find real issues without making every run a whole-codebase audit.

## Permission Graduation

Begin with the smallest useful output:

```text
Level 1: contents: read
  -> report artifact only

Level 2: contents: read, issues: write
  -> create issues for new actionable findings

Level 3: contents: read, issues: write, pull-requests: write
  -> create PRs only when the remediation is narrow and reviewable
```

Avoid letting one unattended agent detect a problem, decide the remediation, modify production code, and merge it without an independent checkpoint.

Prefer this flow for higher-risk maintenance:

```text
finding
  ↓
agent creates issue
  ↓
different agent or human investigates
  ↓
fix PR
  ↓
human review
```

## Executor Options

Use the same task definition and domain knowledge where possible, then choose the executor by environment:

| Environment | Starting point |
|---|---|
| Work with approved Anthropic cloud access | Claude Routine |
| Work with restrictive enterprise controls | GitHub Actions invoking Claude Code headlessly with `--bare -p` |
| GitHub-hosted home project with Copilot access | Copilot Automation |
| OpenCode-based project | GitHub Actions invoking OpenCode |
| Cross-harness experiment | GitHub Agentic Workflows (`gh aw`) |

Treat these as adapter choices, not separate concepts. Claude Routines, Copilot Automations, OpenCode GitHub Actions, and GitHub Agentic Workflows differ in runtime and permission model, but the handbook concept is the same: scheduled task definition plus reusable agent knowledge plus controlled executor.

## Maintenance Cadence

A repository maintenance programme can combine cheaper frequent checks with stronger periodic reviews:

| Cadence | Typical model strength | Example jobs |
|---|---|---|
| Daily | cheap model | changed-code smell scan, documentation drift |
| Weekly | stronger model | security review, resilience review, architecture drift, dependency review |
| Monthly | strongest model | whole-system architecture review, technical debt assessment, runbook verification |

The cadence belongs in the scheduled task definition. The expertise belongs in reusable skills such as `security-review`, `resilience-review`, or `sync-skills-with-upstream`.

## When to Use

- A repository has recurring maintenance that is valuable but easy to forget.
- The task can be scoped with clear permissions and output rules.
- The job can compare against previous state or a previous audit commit.
- Findings can go through human-readable reports, issues, or reviewable PRs.
- You want the same maintenance capability to run across Claude, Copilot, OpenCode, or GitHub-hosted automation.

## When Not to Use

- The task requires high-stakes judgment every time and cannot be safely scoped.
- You cannot record state, suppress duplicate findings, or distinguish new issues from old ones.
- The only useful result is direct code modification with no review gate.
- The repository or organization does not permit unattended agent access.
- A deterministic tool such as Dependabot, CodeQL, a linter, or a documentation generator already solves the problem.

## Related

- [Sync Skills With Upstream Repositories](../playbooks/sync-skills-with-upstream.md) - Concrete scheduled maintenance playbook for keeping skills current through PRs.
- [Unified Agent Workspace](unified-agent-workspace.md) - Control-plane placement for shared skills, workflows, generated indexes, and remote automation.
- [Context Management](context-management.md) - Token-efficient focused context selection for recurring analysis.
- [Repository Mental Model Tools](../references/repository-mental-model-tools.md) - Graph and code-intelligence tools that can support incremental review.
- [Review Implementation](../playbooks/review-implementation.md) - Review discipline that scheduled review agents should reuse rather than duplicate.

## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Scheduled agentic maintenance architecture | User-provided synthesis | Practitioner summary | Separates domain knowledge, scheduled task definition, and executor across Claude, Copilot, OpenCode, and GitHub Agentic Workflows | N/A |
| Claude Routines | Claude Code Docs | Documentation | Documents Anthropic-managed Routines that can trigger on schedules, API calls, or GitHub events | https://code.claude.com/docs/en/routines |
| Claude web scheduled tasks | Claude Code Docs | Documentation | Documents cloud-hosted scheduled work that continues when the local computer is off | https://code.claude.com/docs/en/web-scheduled-tasks |
| Claude scheduling levels | Claude Code Docs | Documentation | Distinguishes session `/loop`, desktop scheduled tasks, cloud Routines, and GitHub Actions execution | https://code.claude.com/docs/en/scheduled-tasks |
| Claude headless execution | Claude Code Docs | Documentation | Documents noninteractive `claude -p` execution and `--bare` for CI-safe runs | https://code.claude.com/docs/en/headless |
| Using Copilot Automations | GitHub Docs | Documentation | Documents creating recurring Copilot agent tasks through the GitHub Copilot app | https://docs.github.com/en/copilot/how-tos/github-copilot-app/using-automations |
| Copilot Automations | GitHub Docs | Documentation | Documents recurring Copilot agent tasks with prompts, models, allowed tools, triggers, and repository constraints | https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations |
| OpenCode GitHub Action | OpenCode Docs | Documentation | Documents scheduled OpenCode execution through GitHub Actions | https://opencode.ai/docs/github/ |
| GitHub Agentic Workflows | GitHub Docs | Documentation | Documents Markdown-defined agentic workflows compiled into hardened GitHub Actions and support for multiple agents | https://docs.github.com/en/copilot/concepts/agents/about-github-agentic-workflows |
