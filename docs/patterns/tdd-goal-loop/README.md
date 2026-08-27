# TDD Goal Loop Pattern

**Status:** Composite Pattern  
**Purpose:** Coordinate specialized agents through TDD red/green cycles inside an evidence-driven Goal Loop.  
**Related Patterns:** [Goal Loop](../goal-loop/README.md), [Test-Driven Development](../test-driven-development.md), [Vertical Slicing](../vertical-slicing.md), [Tracer Bullets](../tracer-bullets.md), [Ralph Loop](../ralph-loop/README.md)

---

## Core Concept

A TDD Goal Loop is a specialized Goal Loop for implementation work where progress is made through one or more test-driven vertical slices.

The outer loop proves whether the overall goal is complete. The inner loop uses specialized agents to perform disciplined TDD:

```text
Goal Loop
    |
    v
coordinator selects next vertical slice or tracer bullet
    |
    v
TDD inner loop
    |-- test-writer adds one failing test
    |-- red-verifier confirms expected failure
    |-- code-writer implements minimum production code
    |-- green-verifier confirms passing test and relevant regressions
    |-- repeat until the slice is complete
    |
    v
slice-verifier confirms the vertical slice is complete
    |
    v
goal-evaluator compares accumulated evidence to the original goal
```

Use this pattern when one generic executor is too broad. The coordinator conducts specialized executors and verifiers, while the evaluator remains separate from implementation.

---

## When to Use

Use a TDD Goal Loop when:

- The goal is implementation-heavy and can be proven with tests.
- The work can be decomposed into vertical slices or tracer bullets.
- You want tests written before production code.
- Multiple agent roles are likely to improve discipline, such as when requirements are ambiguous or implementation visibility may bias test selection.
- You need explicit red-phase and green-phase evidence before evaluating goal completion.

Do not use it when:

- The task is too small for role orchestration.
- The feature is well specified and a single agent can run a deterministic TDD gate cheaply.
- The goal is exploratory and success criteria are not testable yet.
- TDD would create artificial tests for documentation-only, configuration-only, or research-only work.
- A human must choose product behavior before tests can be written.

Default to a single TDD loop plus a deterministic gate for well-specified features. Escalate to separate test-writer and code-writer contexts only when the added isolation is likely to catch confirmation bias, ambiguous scope interpretation, or implementation-led testing.

---

## Role Model

| Role | Writes Tests? | Writes Production Code? | Runs Checks? | Decides Goal Completion? |
|---|---|---|---|---|
| Coordinator | Usually no | Usually no | Delegates | No |
| Slice planner | No | No | No | No |
| Test writer | Yes | No | Maybe targeted discovery | No |
| Red verifier | No | No | Yes, preferably deterministic | No |
| Code writer | No | Yes | Maybe targeted tests | No |
| Green verifier | No | No | Yes, preferably deterministic | No |
| Slice verifier | No | No | Yes, preferably deterministic | No |
| Goal evaluator | No | No | Maybe inspect evidence | Yes |

The key separation is:

```text
test-writer != code-writer != verifier != evaluator
```

The evaluator can judge completion only from surfaced evidence: failing-test output, passing-test output, diffs, acceptance criteria mapping, and remaining work.

Verifier roles do not have to be LLM agents. When a verifier only runs tests, parses counts, checks exit codes, or matches known failure text, implement it as deterministic driver code or shell assertions. Keep LLM judgment for ambiguous work such as writing tests, implementing code, or doing the final spec-versus-evidence review.

---

## Canonical Flow

For each goal turn:

1. Coordinator reads the original goal and current evidence.
2. Slice planner identifies the smallest next vertical slice or tracer bullet.
3. Test writer adds exactly one failing test for the selected behavior.
4. Red verifier runs the targeted test and confirms it fails for the expected reason, using deterministic checks when possible.
5. Code writer implements the minimum production code needed to pass the confirmed failing test.
6. Green verifier confirms the new test passes and checks relevant regressions, using deterministic checks when possible.
7. Coordinator repeats red/green only as needed for the current slice.
8. Slice verifier checks that the slice is complete, observable, scoped, and mapped to acceptance criteria.
9. Goal evaluator compares accumulated slice evidence against the original goal.
10. The outer Goal Loop continues, stops as met, or stops as impossible.

---

## Status Vocabulary

Use specific verifier statuses so the coordinator can route work without conflating claims.

| Agent | Success | Retryable Failure | Blocker |
|---|---|---|---|
| Red verifier | `RED_CONFIRMED` | `RED_INVALID` | `RED_BLOCKED` |
| Green verifier | `GREEN_CONFIRMED` | `GREEN_FAILED` | `GREEN_BLOCKED` |
| Slice verifier | `SLICE_COMPLETE` | `SLICE_INCOMPLETE` | `SLICE_BLOCKED` |
| Goal evaluator | `GOAL_MET` | `GOAL_NOT_MET` | `GOAL_IMPOSSIBLE` |

Routing rules:

| Status | Coordinator Action |
|---|---|
| `RED_CONFIRMED` | Send to code writer |
| `RED_INVALID` | Send feedback to test writer |
| `GREEN_FAILED` | Send feedback to code writer |
| `SLICE_INCOMPLETE` | Continue the current slice or choose the next test |
| `GOAL_NOT_MET` | Choose the next slice from evaluator feedback |
| `GOAL_IMPOSSIBLE` | Stop and report the blocker |

---

## Evidence Requirements

A TDD Goal Loop should surface:

- The selected vertical slice or tracer bullet.
- The acceptance criterion covered by the slice.
- The test added by the test writer.
- The red-phase command and expected failing output.
- The production code changed by the code writer.
- The green-phase command and passing output.
- Any relevant regression checks.
- The slice verifier's acceptance-criteria mapping.
- The goal evaluator's status and remaining criteria.

If evidence is missing, the evaluator should return `GOAL_NOT_MET` rather than infer completion.

Keep machine state separate from narrative evidence. A small state file can track completed slices for control flow, while the human-readable evidence log remains the audit trail. Avoid making every agent re-read a growing prose log to recover one routing fact.

---

## Token-Efficient Control

The multi-agent value comes from role isolation, not from using an LLM for every role. The working rule is: judgment gets an agent; facts get a script.

For well-specified work, prefer a single agent running a deterministic gate. For ambiguous work, preserve these boundaries:

- Test writer cannot see or modify production code beyond what is needed to place the failing test.
- Code writer receives the failing test and failure output, not the entire original context.
- Goal evaluator runs once at the end or at explicit checkpoints with the full spec and accumulated evidence.

Prefer this control shape when token cost matters:

```text
for each slice:
    driver selects next incomplete slice from machine state
    test-writer receives only criterion, target test file, conventions
    driver runs red check and asserts expected failure shape
    code-writer receives failing test and red output
    driver runs green check and asserts passing result
    driver appends evidence and updates state

goal-evaluator reviews full spec and evidence once
```

Use LLM verifier agents only when the failure shape cannot be expressed as an exit-code, test-count, or output assertion.

If deterministic gates replace verifier agents, test the gates themselves. The risk moves out of the LLM and into the script. Gate self-tests should cover skipped tests, deleted tests, shrinking test counts, compile failures, expected red failures, unexpected reds, and final green success. Mutation-testing the gate with deliberately broken gate versions is a useful way to prove the self-test suite fails when the gate is wrong.

---

## Relationship to Other Patterns

| Pattern | Relationship |
|---|---|
| [Goal Loop](../goal-loop/README.md) | Provides the outer completion-proof loop and evaluator statuses |
| [Test-Driven Development](../test-driven-development.md) | Provides the red/green/refactor implementation discipline |
| [Vertical Slicing](../vertical-slicing.md) | Defines the bounded unit of progress |
| [Tracer Bullets](../tracer-bullets.md) | Defines the minimal end-to-end implementation shape for a slice |
| [Ralph Loop](../ralph-loop/README.md) | Can use TDD Goal Loop as the inner implementation/control structure for an issue |

---

## Common Pitfalls

### Pitfall 1: Test Writer Implements Production Code

**Problem:** The red phase is compromised because the test writer also edits implementation.

**Prevention:** Restrict the test writer to tests and fixtures. Production code belongs to the code writer after red verification.

### Pitfall 2: Code Writer Weakens Tests

**Problem:** The green phase succeeds by changing the test instead of implementing behavior.

**Prevention:** Code writer should not edit tests unless the coordinator explicitly routes invalid-test feedback back through the test-writing role.

### Pitfall 3: Passing Tests Treated as Goal Completion

**Problem:** The green verifier confirms tests pass, but the original acceptance criteria are still incomplete.

**Prevention:** Keep green verification separate from slice verification and goal evaluation.

### Pitfall 4: Slice Becomes Too Large

**Problem:** The coordinator lets one slice absorb unrelated requirements.

**Prevention:** Select one tracer-bullet behavior at a time. Defer refinements into later slices unless they are required by the current acceptance criterion.

### Pitfall 5: Agents Re-Read Shared Context For Deterministic Work

**Problem:** Planner and verifier agents repeatedly read the same specification, evidence log, and slice list to make decisions a driver could make deterministically.

**Prevention:** Push only the needed slice context down to test and code agents. Keep state in machine-readable form. Replace routine verifier agents with command execution and assertions.

### Pitfall 6: Context Isolation Becomes The Default Architecture

**Problem:** Separate test-writer and code-writer agents multiply prompt construction, cold starts, and gate invocations even when requirements are already precise enough for a single loop.

**Prevention:** Use the single loop as the default. Escalate to isolated agents only when ambiguity or confirmation-bias risk justifies the extra cost.

### Pitfall 7: The Gate Accepts A False Green

**Problem:** Once verifier agents are replaced by a deterministic gate, any bug in the gate can silently approve bad TDD evidence. For example, some test runners include skipped tests in headline test counts.

**Prevention:** Treat the gate as production test infrastructure. Add gate self-tests and include cases for skipped tests, deleted tests, and shrinking suites.

---

## Sources and Provenance

| Concept/Section | Source | Type | Context | Evidence |
|---|---|---|---|---|
| TDD Goal Loop | Internal synthesis from handbook maintenance discussion | Conversation / design note | Combined Goal Loop role separation with TDD red/green cycles, vertical slicing, tracer bullets, and specialized agent orchestration | Added as composite pattern with Claude adapter |
| TDD cycle | Established TDD practice | Industry practice | Red -> green -> refactor cycle with tests written before implementation | See [Test-Driven Development](../test-driven-development.md) |
| Vertical slice boundary | Existing handbook pattern | Pattern composition | Uses slices as bounded, independently testable implementation units | See [Vertical Slicing](../vertical-slicing.md) |
| Tracer bullet implementation | Existing handbook pattern | Pattern composition | Uses minimal end-to-end behavior as the first slice shape | See [Tracer Bullets](../tracer-bullets.md) |
| Token-efficient verification | TDD Goal Loop Claude Spring Boot Lab run analysis | Experiment | Measured a run where verifier/planner/evaluator roles consumed most tokens and deterministic checks would preserve role isolation at lower cost | See [lab playbook](../../playbooks/tdd-goal-loop-claude-spring-boot-lab.md#lab-6-token-efficient-control-variant) |
| Single-loop default and isolation escalation | TDD Goal Loop Claude Spring Boot Lab isolation experiment | Experiment | Compared isolated test/code agents against a single-loop agent using a deterministic gate and pre-registered mutants; both killed 15/16 pre-registered mutants, while isolation cost 3.9x more subagent tokens | See [lab playbook](../../playbooks/tdd-goal-loop-claude-spring-boot-lab.md#lab-7-isolation-versus-single-loop-experiment) |

---

## See Also

- [TDD Goal Loop Claude Spring Boot Lab](../../playbooks/tdd-goal-loop-claude-spring-boot-lab.md) - Fully implementable Maven/Spring Boot prototype lab for proving this pattern in Claude Code.
- [Claude Adapter](adapters/claude.md) - Claude Code subagent mapping for coordinator, test writer, code writer, verifiers, and evaluator.
- [Goal Loop Claude Adapter](../goal-loop/adapters/claude.md) - Native `/goal` behavior and stronger hook/controller options.
- [Software Development Process](../../processes/software-development.md#42-afk-implementation-ralph-loop) - AFK implementation context where this pattern can be applied.
