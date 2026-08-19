# Goal Loop: Claude Adapter

**Harness:** Claude Code  
**Prerequisite:** [Goal Loop Pattern (Core Concept)](../README.md)

---

## Overview

When Claude Code provides native `/goal` behavior, prefer it over a prompt-only loop. The important difference is that the harness can enforce continuation after an evaluator checks the goal, instead of relying only on the model to decide whether to keep working.

Claude Code's official `/goal` command sets a session-scoped completion condition. Claude Code starts a turn immediately, evaluates the condition after each turn with a small fast model, and either continues, clears the goal as achieved, or clears it as impossible.

---

## Mapping

```text
Goal Loop
    │
    ▼
Claude Code native /goal
    │
    ├─ implementation turns
    ├─ independent evaluation
    └─ harness-controlled continuation
```

Use native `/goal` when:

- The completion condition is explicit.
- The harness can evaluate progress independently between turns.
- You want the agent to continue until the goal is met, impossible, or bounded by resource limits.
- The work is substantial and has a verifiable end state, such as tests passing, acceptance criteria holding, file-size budgets being met, or a labeled backlog being emptied.

---

## Claude Code Behavior

Claude Code documents `/goal` as a condition-driven session loop:

- One goal can be active per session.
- Running `/goal <condition>` replaces any active goal and starts work immediately.
- Running `/goal` with no arguments shows goal status, evaluator reason, turn count, duration, and token spend.
- Running `/goal clear` removes the active goal; `stop`, `off`, `reset`, `none`, and `cancel` are aliases.
- Active goals can resume with `--resume` or `--continue`; achieved or cleared goals are not restored.
- `/goal` works in non-interactive mode, desktop app, and Remote Control.

Evaluator outcomes map directly to the canonical Goal Loop statuses:

| Claude Code Verdict | Goal Loop Status | Behavior |
|---|---|---|
| Not yet met | `GOAL_NOT_MET` | Claude continues and uses the evaluator reason as next-turn guidance |
| Met | `GOAL_MET` | Claude Code clears the goal and records it as achieved |
| Impossible | `GOAL_IMPOSSIBLE` | Claude Code clears the goal and records failure with the reason |

The evaluator does not run tools or inspect files independently. It judges the condition against evidence surfaced in the conversation, so the implementation turn must run checks and include relevant outputs.

---

## Prompt Guidance

Even with native support, state the completion condition in evidence-based terms:

```text
/goal Implement feature #27 and satisfy its Definition of Done.

Completion evidence must include:
- relevant tests passing
- acceptance criteria mapped to implementation
- no unexplained failing checks
- summary of changed files
```

Avoid goals such as "make this better" or "clean this up" unless you first define what evidence will prove completion.

For issue-level work, a GitHub issue with acceptance criteria is a practical Goal Loop target when the criteria are testable and the condition says how to prove completion:

```text
/goal Complete GitHub issue #42.

Completion evidence must include:
- every acceptance criterion from issue #42 mapped to an implementation change or explicit non-code resolution
- relevant tests and lint/build checks passing
- no unrelated files modified beyond the issue scope
- final summary identifying changed files and verification commands run
```

This is viable because it matches Claude Code's guidance to use goals for substantial work with a verifiable end state. If the issue lacks acceptance criteria or the checks are unclear, first refine the issue rather than starting `/goal`.

Write conditions so the evaluator can judge from the transcript:

- Prefer one measurable end state.
- State the check that proves it, such as `npm test exits 0`.
- Include important constraints, such as `no other test file is modified`.
- Add a bound when needed, such as `or stop after 20 turns`.

To run non-interactively:

```bash
claude -p "/goal CHANGELOG.md has an entry for every PR merged this week"
```

For long-running non-interactive goals, use streaming output so progress is visible:

```bash
claude -p "/goal all tests in test/auth pass and lint is clean" --output-format stream-json --verbose
```

Use auto mode when goal turns should run unattended. Auto mode approves tool calls within turns; `/goal` supplies the separate after-turn evaluator and continuation behavior.

---

## Orchestrated Subagents

Native `/goal` can use custom subagents inside each implementation turn, but the final continuation decision still belongs to Claude Code's built-in `/goal` evaluator.

Use this structure when you want explicit Goal Loop roles while keeping native `/goal` continuation:

```text
/goal loop
    ├─ main/coordinator Claude turn
    │   ├─ calls custom executor subagent
    │   ├─ calls custom verifier subagent
    │   └─ calls custom evaluator subagent
    └─ Claude Code built-in /goal evaluator decides continue/met/impossible
```

Project-level subagents live under `.claude/agents/` and can be checked into the repository:

```text
.claude/
├── agents/
│   ├── goal-executor.md
│   ├── goal-verifier.md
│   └── goal-evaluator.md
```

Example executor:

```markdown
---
name: goal-executor
description: Implements focused code changes toward an explicit goal. Use for implementation work only.
tools: Read, Grep, Glob, Edit, Write, Bash
model: sonnet
---

You are the Goal Loop executor.

Your job:
- make focused progress toward the stated goal
- avoid declaring completion
- keep changes scoped
- report what changed and what remains

Do not evaluate final success.
```

Example verifier:

```markdown
---
name: goal-verifier
description: Runs checks and gathers evidence for a goal. Use after implementation changes.
tools: Read, Grep, Glob, Bash
model: sonnet
---

You are the Goal Loop verifier.

Your job:
- run the stated verification commands
- inspect relevant repository state
- collect evidence from tests, diffs, build output, and acceptance criteria
- report facts only

Do not edit files.
Do not decide final success.
```

Example evaluator:

```markdown
---
name: goal-evaluator
description: Independently evaluates whether a stated goal is satisfied by evidence. Use after verification.
tools: Read, Grep, Glob, Bash
model: haiku
---

You are the Goal Loop evaluator.

Compare the original goal against the evidence.

Return exactly one status:
- GOAL_MET
- GOAL_NOT_MET
- GOAL_IMPOSSIBLE

Explain the reason briefly.
Do not edit files.
Do not implement fixes.
```

Invoke `/goal` with orchestration instructions so the coordinator knows to delegate each role:

```text
/goal Complete GitHub issue #42.

During each turn:
1. Use goal-executor to make focused progress.
2. Use goal-verifier to gather evidence.
3. Use goal-evaluator to judge the evidence.
4. Surface the evaluator status and evidence in the transcript.

Completion evidence must include:
- every acceptance criterion mapped to implementation or explicit non-code resolution
- relevant tests passing
- lint/build checks passing if applicable
- no unrelated files modified
- final changed-file summary
```

Expected flow:

```text
User sets /goal
    │
    ▼
Claude turn 1
    ├─ goal-executor makes progress
    ├─ goal-verifier gathers evidence
    └─ goal-evaluator returns GOAL_NOT_MET with reason
    │
    ▼
Claude Code /goal evaluator sees transcript evidence and continues
    │
    ▼
Claude turn 2
    ├─ goal-executor addresses missing condition
    ├─ goal-verifier reruns checks
    └─ goal-evaluator returns GOAL_MET
    │
    ▼
Claude Code /goal evaluator clears the goal as achieved
```

This gives strong role separation inside the turn, but it is still advisory to the native `/goal` evaluator. The custom `goal-evaluator` helps produce evidence and a judgment in the transcript; it does not replace Claude Code's built-in `/goal` evaluator.

---

## Custom Control Decision

If the custom evaluator must be the actual stop/continue authority, do not rely on native `/goal` alone. Use one of these stronger mechanisms:

| Mechanism | Use When | Control Boundary |
|---|---|---|
| Stop hook | You want after-turn continuation governed by your own script or prompt | Hook decides whether Claude may stop |
| Agent hook | You need multi-turn or tool-using verification inside the hook path | Hook-spawned agent verifies and returns a decision |
| External controller | You want a Ralph-style shell/API loop outside Claude Code | Controller invokes Claude, gathers evidence, and decides continue/stop |

Conceptual stronger flow:

```text
Claude turn finishes
    │
    ▼
custom Stop hook / controller
    │
    ├─ custom evaluator returns GOAL_MET ───────► allow stop
    ├─ custom evaluator returns GOAL_NOT_MET ───► continue with feedback
    └─ custom evaluator returns GOAL_IMPOSSIBLE ► stop and report blocker
```

Choose native `/goal` with subagents for convenience and session-native continuation. Choose a Stop hook, agent hook, or external controller when custom evaluator output must be authoritative.

---

## Requirements and Limits

- `/goal` follows Claude Code's workspace trust and hooks settings rules.
- `/goal` is unavailable when hooks are disabled by `disableAllHooks` or restricted by managed settings such as `allowManagedHooksOnly`.
- Evaluation uses the configured small fast model, defaulting to the provider's Haiku-equivalent behavior where applicable.
- The evaluator's tokens are billed separately, though usually small relative to implementation turns.
- If Claude repeatedly responds without making progress or tool use, Claude Code can stop the loop and return control with the goal still set.
- Subagents run in their own contexts with their own prompts, tool access, model settings, and permissions.
- Hooks can be configured globally, per project, in skills, or in subagent frontmatter; hook settings and organization policies affect whether custom control mechanisms are available.

---

## Sources

- [Claude Code `/goal` documentation](https://code.claude.com/docs/en/goal) — Official behavior, usage guidance, evaluator model, non-interactive mode, and requirements.
- [Claude Code subagents documentation](https://code.claude.com/docs/en/sub-agents) — Custom subagent files, tool restrictions, model selection, permissions, and project/user scopes.
- [Claude Code hooks guide](https://code.claude.com/docs/en/hooks-guide) and [hooks reference](https://code.claude.com/docs/en/hooks) — Stop hooks, prompt hooks, agent hooks, hook lifecycle, and custom control points.

---

## See Also

- [Goal Loop Pattern](../README.md) — Harness-agnostic core pattern.
- [OpenCode Adapter](opencode.md) — Prompt-driven and controller-based OpenCode mappings.
- [Ralph Loop Claude Adapter](../../ralph-loop/adapters/claude.md) — Claude guidance for Ralph Loop implementation work.
- [Configure Claude Code Hooks](../../../playbooks/configure-claude-code-hooks.md) — Concrete Claude Code hook recipes for routing, guardrails, and quality checks.
- [Claude Code Hooks Adapter](../../../adapters/claude-code-hooks/README.md) — Organized hook example library.
