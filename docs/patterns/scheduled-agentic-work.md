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
├── AGENTS.md
├── .agents/
│   ├── skills/
│   │   ├── security-review/
│   │   └── resilience-review/
│   └── prompts/
│       ├── scheduled-security-review.md
│       └── scheduled-resilience-review.md
├── skills-lock.json
├── agents-lock.json
├── scripts/
│   └── bootstrap-agents.sh
└── .github/
    └── workflows/
```

The scheduled prompt should stay small. For example, it should say `Use the resilience-review skill to analyse changes since the previous review`, not embed the full resilience checklist.

## Runtime Portability

Scheduled and cloud agents only see what exists in their execution environment. A skill, subagent, MCP configuration, or custom prompt that exists only under a developer's user profile will not automatically exist inside a fresh GitHub Actions runner, Copilot cloud agent, Claude cloud task, or other hosted executor.

Separate source of truth from installed runtime material:

```text
skills repository / plugin
        ↓
version, tag, or commit
        ↓
skills-lock.json / agents-lock.json
        ↓
bootstrap step
        ├─ installs skills
        ├─ installs agents
        └─ verifies expected versions
        ↓
scheduled agent
```

For unattended work, make this bootstrap part of the scheduled workflow itself:

```text
fresh runner
  ↓
checkout repository
  ↓
read AGENTS.md
  ↓
install locked skills and agents
  ↓
validate environment
  ↓
invoke named agent
  ↓
named agent invokes named skill
```

Use this ownership rule:

```text
Commit intent; lock dependencies.
```

Repository-owned skills that describe how this repository should be reviewed, operated, documented, or changed are development policy and should usually be committed. Generic third-party skills and agents are dependencies and can be reconstructed from a committed lock file.

```text
.agents/
├── skills/
│   ├── security-review/        # committed repository intent
│   ├── resilience-review/      # committed repository intent
│   └── project-documentation/  # committed repository intent
└── external/
    ├── vendor-skill/...        # ignored installed dependency
    └── reusable-tooling/...    # ignored installed dependency

skills-lock.json                # committed dependency inventory
agents-lock.json                # committed dependency inventory when needed
```

Do not rely on user-level paths such as `~/.claude`, `~/.copilot`, `~/.config/opencode`, or similar directories for scheduled work unless the scheduled environment explicitly bootstraps them.

## Agent and Skill Packaging

Skills are the most portable unit. Prefer `.agents/skills/` for repository-owned skills because multiple agent harnesses can discover project skills there or can be bootstrapped to use that location.

Custom agents are more harness-specific. Keep the substantive role instructions in a shared repository file, then make vendor-specific agent wrappers thin:

```text
agents/
└── common/
    ├── security-reviewer.md
    └── resilience-reviewer.md

.github/
└── agents/
    └── resilience-reviewer.agent.md

.claude/
└── agents/
    └── resilience-reviewer.md

.opencode/
└── agents/
    └── resilience-reviewer.md
```

Each wrapper should point at the shared role definition and required skills rather than duplicating the whole role. This keeps the behavior coherent when the same scheduled job runs through Copilot, Claude, OpenCode, or GitHub Agentic Workflows.

For deterministic scheduled work, name the required capability explicitly:

```text
Use the `resilience-reviewer` agent.
The `resilience-reviewer` agent must use the `resilience-review` and `code-review` skills.
If either capability is unavailable, fail instead of falling back to a generic review.
```

Automatic skill discovery is useful for interactive work. Scheduled work should fail closed when required specialist capabilities are unavailable.

## Preflight Validation

Every scheduled workflow that depends on skills or custom agents should run a preflight after bootstrap and before analysis:

```text
Expected capabilities:

[x] security-review skill
[x] resilience-review skill
[x] code-review skill
[x] resilience-reviewer agent

Expected versions:

security-review       1.4.2
resilience-review     0.8.1
code-review           2.1.0
```

If a required capability or version is missing, fail the job. Do not let the general-purpose model improvise a replacement and report success.

## Design Rules

- Keep domain expertise in skills, `AGENTS.md`, standards, and playbooks.
- Keep schedule, trigger, scope, state, and output policy in the task definition.
- Keep executor glue in adapters, GitHub Actions, Routines, Automations, or agentic workflow tooling.
- Commit repository-owned skills and shared agent instructions when they encode repository policy.
- Lock and bootstrap externally sourced skills and agents when they are dependencies.
- Validate required agents, skills, and versions before running scheduled analysis.
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

### Claude Code

Claude has multiple scheduling levels with different persistence and control boundaries:

| Level | Use when |
|---|---|
| `/loop` | You need temporary polling inside the current session. |
| Desktop scheduled task | You want a persistent local task that runs while the desktop app is available. |
| Claude Routine | Anthropic-managed cloud execution is approved and the job should continue when your machine is off. |
| GitHub Actions plus `claude --bare -p` | Enterprise controls require execution inside approved CI runners. |

For CI, prefer headless execution with `--bare` so the run does not accidentally load machine-specific hooks, MCP configuration, or memory. Keep allowed tools narrow, for example read-only repository access at first.

### GitHub Copilot

Copilot Automations define recurring cloud agent tasks with a prompt, model, allowed tools, and trigger. Use them when the repository is already on GitHub and the plan, repository visibility, and organization settings allow Copilot cloud agents.

Copilot is often the lowest-friction home-project option because the scheduling, repository access, issue creation, labels, and PR creation live inside GitHub's permission model.

### OpenCode

OpenCode currently fits naturally behind GitHub Actions scheduling:

```text
GitHub cron
  ↓
GitHub Actions runner
  ↓
OpenCode
  ↓
chosen model
```

This keeps scheduling in GitHub and lets OpenCode choose the model appropriate to the job. Cheap models can handle frequent low-risk scans; stronger models can be reserved for security, resilience, or architecture reviews.

### GitHub Agentic Workflows

GitHub Agentic Workflows (`gh aw`) let a Markdown task definition compile into a hardened GitHub Actions workflow. This is closest to the handbook architecture because the task definition can stay harness-neutral while GitHub selects or configures an agent executor.

GitHub documents support for multiple agents, including Claude Code, Copilot CLI, OpenAI Codex, and Gemini CLI. OpenCode may still need its own direct GitHub Action integration until it is supported as a `gh aw` executor.

Use this shape for cross-harness experiments:

```text
agentic task definition
  ↓
GitHub Agentic Workflows
  ↓
GitHub Actions
  ├─ Claude Code
  ├─ Copilot CLI
  └─ other supported agent
```

## Example Task Definition

A scheduled task definition should point to repository knowledge instead of embedding all expertise:

```markdown
---
on:
  schedule:
    - cron: "0 6 * * 1"
permissions:
  contents: read
  issues: write
---

Review this repository for resilience weaknesses.

Use the `resilience-reviewer` agent and the `resilience-review` skill.

Do not perform the review unless both capabilities are available.

Only report actionable findings.

Determine the commit used by the previous resilience audit and analyze changes since that commit.
Expand beyond changed files only where dependency or graph information shows connected components are affected.

For each new HIGH finding, create an issue with:
- failure scenario
- likelihood
- blast radius
- evidence
- suggested mitigation

If nothing significant changed, take no action.
```

The reusable `resilience-review` skill can hold the actual domain checklist: timeouts, retries, retry storms, circuit breakers, connection pools, Kafka delivery semantics, idempotency, transaction boundaries, outbox usage, dead-letter handling, health checks, Kubernetes termination, resource limits, database locking, race conditions, backpressure, and observability gaps.

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
- Required skills and agents can be committed, locked, bootstrapped, and validated in the execution environment.
- The job can compare against previous state or a previous audit commit.
- Findings can go through human-readable reports, issues, or reviewable PRs.
- You want the same maintenance capability to run across Claude, Copilot, OpenCode, or GitHub-hosted automation.

## When Not to Use

- The task requires high-stakes judgment every time and cannot be safely scoped.
- You cannot record state, suppress duplicate findings, or distinguish new issues from old ones.
- Required skills or agents exist only in a developer's user-level configuration and cannot be reproduced in CI or the cloud executor.
- The only useful result is direct code modification with no review gate.
- The repository or organization does not permit unattended agent access.
- A deterministic tool such as Dependabot, CodeQL, a linter, or a documentation generator already solves the problem.

## Related

- [Sync Skills With Upstream Repositories](../playbooks/sync-skills-with-upstream.md) - Concrete scheduled maintenance playbook for keeping skills current through PRs.
- [Agent Workspace Lab](../playbooks/agent-workspace-lab.md) - Staged lab for proving skills, agents, bootstrap, fresh-clone portability, CI, and scheduled custodians.
- [Unified Agent Workspace](unified-agent-workspace.md) - Control-plane placement for shared skills, workflows, generated indexes, and remote automation.
- [Context Management](context-management.md) - Token-efficient focused context selection for recurring analysis.
- [Repository Mental Model Tools](../references/repository-mental-model-tools.md) - Graph and code-intelligence tools that can support incremental review.
- [Review Implementation](../playbooks/review-implementation.md) - Review discipline that scheduled review agents should reuse rather than duplicate.

## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Scheduled agentic maintenance architecture | User-provided synthesis | Practitioner summary | Separates domain knowledge, scheduled task definition, and executor across Claude, Copilot, OpenCode, and GitHub Agentic Workflows | N/A |
| CI-portable agent and skill runtime | User-provided synthesis | Practitioner summary | Defines commit intent / lock dependencies, bootstrap, preflight validation, thin harness-specific agent wrappers, and fail-closed scheduled execution | N/A |
| Claude Routines | Claude Code Docs | Documentation | Documents Anthropic-managed Routines that can trigger on schedules, API calls, or GitHub events | https://code.claude.com/docs/en/routines |
| Claude web scheduled tasks | Claude Code Docs | Documentation | Documents cloud-hosted scheduled work that continues when the local computer is off | https://code.claude.com/docs/en/web-scheduled-tasks |
| Claude scheduling levels | Claude Code Docs | Documentation | Distinguishes session `/loop`, desktop scheduled tasks, cloud Routines, and GitHub Actions execution | https://code.claude.com/docs/en/scheduled-tasks |
| Claude headless execution | Claude Code Docs | Documentation | Documents noninteractive `claude -p` execution and `--bare` for CI-safe runs | https://code.claude.com/docs/en/headless |
| Using Copilot Automations | GitHub Docs | Documentation | Documents creating recurring Copilot agent tasks through the GitHub Copilot app | https://docs.github.com/en/copilot/how-tos/github-copilot-app/using-automations |
| Copilot Automations | GitHub Docs | Documentation | Documents recurring Copilot agent tasks with prompts, models, allowed tools, triggers, and repository constraints | https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations |
| Copilot skills | GitHub Docs | Documentation | Documents project and personal skills locations for Copilot agent capabilities | https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills |
| Copilot custom agents | GitHub Docs | Documentation | Documents repository, user, and organization custom agent locations for Copilot | https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/invoke-custom-agents |
| OpenCode skills | OpenCode Docs | Documentation | Documents project and user skill discovery locations for OpenCode | https://opencode.ai/docs/skills/ |
| OpenCode GitHub Action | OpenCode Docs | Documentation | Documents scheduled OpenCode execution through GitHub Actions | https://opencode.ai/docs/github/ |
| GitHub Agentic Workflows | GitHub Docs | Documentation | Documents Markdown-defined agentic workflows compiled into hardened GitHub Actions and support for multiple agents | https://docs.github.com/en/copilot/concepts/agents/about-github-agentic-workflows |
