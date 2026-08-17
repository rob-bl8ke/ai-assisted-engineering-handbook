# Goal Loop: Claude Adapter

**Harness:** Claude Code  
**Prerequisite:** [Goal Loop Pattern (Core Concept)](../README.md)

---

## Overview

When Claude Code provides native `/goal` behavior, prefer it over a prompt-only loop. The important difference is that the harness can enforce continuation after an evaluator checks the goal, instead of relying only on the model to decide whether to keep working.

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

---

## See Also

- [Goal Loop Pattern](../README.md) — Harness-agnostic core pattern.
- [OpenCode Adapter](opencode.md) — Prompt-driven and controller-based OpenCode mappings.
- [Ralph Loop Claude Adapter](../../ralph-loop/adapters/claude.md) — Claude guidance for Ralph Loop implementation work.
