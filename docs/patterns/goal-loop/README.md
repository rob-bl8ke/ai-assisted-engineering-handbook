# Goal Loop Pattern

**Status:** Core Pattern  
**Purpose:** Drive work until a stated completion condition has been demonstrated with evidence.  
**Related Patterns:** [Vertical Slicing](../vertical-slicing.md), [Ralph Loop](../ralph-loop/README.md), [TDD Goal Loop](../tdd-goal-loop/README.md), [Feedback Loops](../feedback-loops.md), [Test-Driven Development](../test-driven-development.md)

---

## Core Concept

A Goal Loop is an evidence-driven control loop for AI-assisted work. It separates the agent that advances the work from the evaluator that decides whether the goal has actually been met.

The central rule is:

```text
actor != judge
```

The implementation agent can write code, inspect files, run tests, and summarize progress. The evaluator should not implement fixes. It only compares the original goal and current evidence against the completion condition.

---

## When to Use

Use a Goal Loop when:

- The goal has a clear completion condition or Definition of Done.
- Completion can be demonstrated through repository state, tests, checks, diffs, or other observable evidence.
- You want the agent to continue after partial progress rather than stop after the first plausible implementation.
- You need a smaller completion-proof loop inside a broader workflow such as [Ralph Loop](../ralph-loop/README.md).
- The unit of work is a [vertical slice](../vertical-slicing.md) with bounded scope and independently testable behavior.
- The unit of work is a GitHub issue with acceptance criteria, a Definition of Done, and stated verification commands.

Do not use a Goal Loop when:

- The goal is exploratory and success cannot be evaluated yet.
- The next step requires human judgment, product authority, or architectural decision-making.
- The work is unsafe to continue automatically without review.

---

## Canonical Behavior

Given a completion condition `C`:

```text
repeat:
    executor advances the work
    verifier gathers evidence
    evaluator evaluates C against the evidence

until:
    C == MET
    or C == IMPOSSIBLE
    or a safety/resource limit is reached
```

The loop has three roles:

| Role | Responsibility | Must Not |
|---|---|---|
| Executor | Make progress toward the goal | Declare success without evidence |
| Verifier | Run tests, checks, inspections, or collect artifacts | Treat claims as evidence |
| Evaluator | Decide whether the completion condition is demonstrated | Implement fixes |

---

## Evaluator Outcomes

The evaluator should return one status:

| Status | Meaning | Next Action |
|---|---|---|
| `GOAL_MET` | The evidence demonstrates the goal is satisfied | Stop successfully |
| `GOAL_NOT_MET` | The goal is still unmet or evidence is missing | Continue or address findings |
| `GOAL_IMPOSSIBLE` | The goal cannot be completed under current constraints | Stop and report blocker |

For `GOAL_NOT_MET`, the evaluator should briefly state the missing evidence or unsatisfied condition. It should not infer success from the executor's claims.

---

## Decision Flow

```text
Start (goal received)
    │
    ▼
Executor advances work
    │
    ▼
Verifier gathers evidence
    │
    ▼
Independent evaluator
    │
    ├─ GOAL_MET ──────────► Stop successfully
    │
    ├─ GOAL_NOT_MET ──────► Address findings and continue
    │
    └─ GOAL_IMPOSSIBLE ───► Stop and report blocker
```

---

## Harness Mapping

Each AI harness should map the same conceptual pattern to its strongest available primitive:

| Harness | Mapping |
|---|---|
| Claude Code | Native `/goal` when available; harness-driven continuation and evaluator are preferred |
| OpenCode | Start with a Markdown slash command plus evaluator subagent; mature to a controller/plugin if prompt-driven continuation is insufficient |
| GitHub Copilot | Use an equivalent wrapper or orchestrator when available; otherwise keep the evaluator role explicit in prompts |

The pattern remains harness-agnostic. Harness-specific mechanics belong in adapters.

---

## Relationship to Ralph Loop

A [Ralph Loop](../ralph-loop/README.md) chooses and executes a well-scoped implementation unit. A Goal Loop proves that the current unit's completion condition is actually satisfied.

```text
Ralph / backlog loop
    │ choose next unit of work
    ▼
Coordinator
    │ execute one unit of work
    ▼
Goal Loop
    │ prove completion
    ▼
plan -> RED -> GREEN -> review -> verify -> docs
```

Use Ralph Loop for autonomous issue-level implementation. Use Goal Loop as the inner completion-proof mechanism when you need independent evaluation before the agent stops.

When implementation should follow strict TDD with separate test-writing and code-writing roles, use [TDD Goal Loop](../tdd-goal-loop/README.md) as a specialized Goal Loop composition.

---

## Common Pitfalls

### Pitfall 1: Self-Judging Completion

**Problem:** The executor says it is done because it wrote code or believes the implementation looks correct.

**Prevention:** Require an independent evaluator to compare observable evidence against the original goal.

### Pitfall 2: Prompt-Only Control Loops

**Problem:** A prompt says "keep going," but the harness does not enforce continuation.

**Prevention:** Treat prompt-driven loops as best-effort. Prefer harness-level controllers when reliability matters.

### Pitfall 3: Vague Completion Conditions

**Problem:** The evaluator cannot decide whether the goal is met because success criteria are ambiguous.

**Prevention:** Define completion conditions before starting. If they cannot be defined, return to discovery or design.

### Pitfall 4: Issue Without Verifiable Evidence

**Problem:** A GitHub issue describes desired work but does not say how completion should be proven.

**Prevention:** Convert the issue into a Goal Loop target only when it has explicit acceptance criteria and checks, such as tests passing, a build succeeding, documentation updated, or a checklist satisfied.

---

## Sources and Provenance

| Concept/Section | Source | Type | Context | Evidence |
|---|---|---|---|---|
| Goal Loop Pattern | Internal synthesis from `content.md` | Conversation / design note | Identified `/goal` as a reusable completion-proof loop separate from harness implementations | Integrated as canonical pattern with OpenCode and Claude mappings |
| Claude Code `/goal` | Claude Code documentation | Documentation | Native `/goal` behavior: session-scoped completion condition, evaluator verdicts, verifiable end states, auto-mode relationship, and non-interactive use | https://code.claude.com/docs/en/goal |
| Claude Code role orchestration | Claude Code subagents and hooks documentation | Documentation | Custom subagents can express executor/verifier/evaluator roles inside `/goal`; Stop hooks, agent hooks, or external controllers are needed when custom evaluator output must control continuation | https://code.claude.com/docs/en/sub-agents; https://code.claude.com/docs/en/hooks-guide; https://code.claude.com/docs/en/hooks |
| OpenCode command mapping | OpenCode Documentation | Documentation | Project commands, agents, server/API, and custom tools | See OpenCode command and adapter references |
| Actor/Judge separation | Internal AI-assisted engineering practice | Principle / pattern | Completion should be evaluated independently from implementation | Reflected in evaluator role and outcomes |

---

## Related Concepts and Cross-Links

**Patterns:**
- [Vertical Slicing](../vertical-slicing.md) — Creates bounded, independently testable units that make good Goal Loop targets.
- [Ralph Loop](../ralph-loop/README.md) — Broader autonomous implementation loop that can contain a Goal Loop.
- [TDD Goal Loop](../tdd-goal-loop/README.md) — Specialized Goal Loop for multi-agent red/green vertical-slice implementation.
- [Feedback Loops](../feedback-loops.md) — General feedback-loop foundation.
- [Test-Driven Development](../test-driven-development.md) — Common verification strategy inside implementation work.

**Processes:**
- [AFK Implementation](../../processes/software-development.md#42-afk-implementation-ralph-loop) — Where autonomous implementation uses evidence-driven completion.

**Harness Adapters:**
- [Claude Adapter](adapters/claude.md) — Mapping to native `/goal` behavior when available.
- [OpenCode Adapter](adapters/opencode.md) — Markdown command plus evaluator subagent approach.

**References:**
- [Cross-Link Index](../../references/cross-links.md) — Handbook concept map.
- [Provenance Guide](../../references/provenance.md) — Attribution format.
