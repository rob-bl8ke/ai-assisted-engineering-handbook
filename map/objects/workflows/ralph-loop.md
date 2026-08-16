---
type: object
cluster: workflows
universe: live
status: stub
entity: docs/patterns/ralph-loop/README.md
---

# Ralph Loop

The autonomous agent feedback cycle: receive a task → implement → run tests → if blocked escalate to human, else mark complete. The canonical pattern for unattended AI-assisted implementation.

## Why this shape

Without an explicit feedback cycle, an agent that hits a blocker either halts silently or continues past the failure and produces broken output. The Ralph Loop externalizes the blocker as a human gate, preserving human oversight while enabling autonomous execution for the happy path.

## Shape

- **Canonical definition:** `docs/patterns/ralph-loop/README.md`
- **Harness adapters:** `docs/patterns/ralph-loop/adapters/claude.md`, `copilot.md`, `opencode.md`
- **Platform examples:** `docs/patterns/ralph-loop/examples/bash.md`, `powershell.md`
- Three states for each iteration: `implementing`, `blocked`, `complete`

Citations: `docs/patterns/ralph-loop/README.md`

## Connected to

- **owns:** Adapters and Examples in `docs/patterns/ralph-loop/`
- **owned-by:** Software Development Process Phase 4 (Implementation)
- **joins:** VerticalSlice (the Loop runs on one slice at a time); FeedbackLoop (Loop outcome feeds the broader feedback mechanism)
- **looks-like-but-is-not:** FeedbackLoop — FeedbackLoop is the broader outcome-capture mechanism; Ralph Loop is the specific execution pattern

## If you change this

- **Hits:** All three harness Adapters; both platform Examples; `docs/processes/software-development.md` Phase 4 references; `docs/references/cross-links.md`
- **Does not hit:** Other patterns (TDD, ContextManagement) — they are used *within* the loop but defined independently

## Surfaces

| Surface | Role |
|---|---|
| AI agents | executes the loop using the harness-specific Adapter |
| Humans | monitors for `blocked` state and resolves escalations |

## See

- Source: `docs/patterns/ralph-loop/README.md`
