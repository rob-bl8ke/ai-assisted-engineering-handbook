# Goal Loop: OpenCode Adapter

**Harness:** OpenCode  
**Prerequisite:** [Goal Loop Pattern (Core Concept)](../README.md)

---

## Overview

OpenCode can approximate a Goal Loop with project-level slash commands and a reusable evaluator subagent. This is useful as a lightweight `/goal` implementation, but it is not as strong as a harness-enforced controller because a Markdown command is still a prompt template.

Use this adapter in two stages:

1. Start with prompt-driven `/goal-lite` using `.opencode/commands/goal.md` and `.opencode/agents/goal-evaluator.md`.
2. Move to a deterministic controller or plugin if you need guaranteed continuation between turns.

---

## Minimal Project Structure

```text
.opencode/
├── commands/
│   └── goal.md
└── agents/
    └── goal-evaluator.md
```

---

## Command Template

Create `.opencode/commands/goal.md`:

```markdown
---
description: Work autonomously until a verifiable goal is satisfied
---

Goal:

$ARGUMENTS

Work toward this goal iteratively.

After each meaningful implementation step:

1. Inspect the current repository state.
2. Run the relevant tests, checks, or verification commands.
3. Compare the evidence against the stated goal.
4. If the goal is not yet demonstrably satisfied, continue working.
5. Do not declare completion merely because the implementation appears correct.

Completion requires evidence.

Before finishing, ask the goal-evaluator subagent to independently evaluate:

- the original goal
- the current repository state
- test/check results
- relevant git diff

The evaluator must return exactly one status:

GOAL_MET
GOAL_NOT_MET
GOAL_IMPOSSIBLE

If GOAL_NOT_MET:

- address its findings
- verify again
- repeat evaluation

Only stop successfully when the evaluator returns GOAL_MET.
```

Usage:

```text
/goal Implement feature #27 and satisfy its Definition of Done
```

---

## Evaluator Agent

Create `.opencode/agents/goal-evaluator.md`:

```markdown
---
description: Independently determines whether a declared goal has been demonstrated
mode: subagent
---

You are a completion evaluator.

You do not implement fixes.

Given:

- a stated goal
- repository state
- git diff
- verification/test evidence

determine whether the goal has been demonstrated.

Return exactly one:

GOAL_MET
GOAL_NOT_MET
GOAL_IMPOSSIBLE

For GOAL_NOT_MET, briefly state the missing evidence or unsatisfied condition.

Do not infer success from claims made by the implementation agent.
Require observable evidence.
```

Keep this evaluator deliberately narrow. It should be a judge, not another implementer.

---

## Control-Loop Limitation

A Markdown command cannot guarantee an actual control loop. It can instruct the model to continue and to repeatedly invoke an evaluator, but continuation still depends on model behavior.

```text
prompt says "keep going"
        ↓
model decides whether to keep going
```

This is weaker than a harness-level controller:

```text
turn ends
   ↓
external evaluator
   ↓
harness decides
   ↓
force another turn
```

Treat the Markdown version as `/goal-lite`, even if exposed to users as `/goal`.

---

## Stronger OpenCode Runtime

For a more reliable implementation, build a controller that drives OpenCode sessions programmatically:

```text
/goal <condition>
       │
       ▼
goal controller
       │
       ▼
implementation turn
       │
       ▼
independent evaluator
       │
       ├─ MET ───────► stop
       ├─ NOT_MET ───► next turn
       └─ IMPOSSIBLE ► stop/report
```

The controller is the piece that enforces continuation. It can live outside language/framework skills so the methodology remains reusable.

### Scripted Controller Shape

This stronger runtime can be implemented with the same general shape as Ralph-style AFK shell controllers: a script repeatedly invokes the coding agent, captures output, inspects repository state, and decides whether to continue.

For Goal Loop, the controller should not rely on the implementation agent saying "complete." It should gather evidence and ask an independent evaluator to decide.

```text
for each iteration:
    run OpenCode implementation turn
    gather evidence:
        - test/check output
        - git diff
        - git status
        - implementation log
    run independent evaluator
    if evaluator returns GOAL_MET:
        stop successfully
    if evaluator returns GOAL_IMPOSSIBLE:
        stop and report blocker
    otherwise:
        continue with evaluator feedback
```

Illustrative Bash sketch:

```bash
#!/usr/bin/env bash
set -euo pipefail

GOAL="${1:-}"
MAX_ITERATIONS="${2:-10}"
VERIFY_COMMAND="${VERIFY_COMMAND:-npm test}"

if [ -z "$GOAL" ]; then
  echo "Usage: $0 <goal> [max-iterations]"
  exit 1
fi

mkdir -p .goal-loop

for i in $(seq 1 "$MAX_ITERATIONS"); do
  echo "------- GOAL ITERATION $i --------"

  implementation_log=".goal-loop/implementation-$i.log"
  verification_log=".goal-loop/verification-$i.log"
  diff_file=".goal-loop/diff-$i.patch"
  evaluation_file=".goal-loop/evaluation-$i.txt"

  opencode run "
Goal:

$GOAL

Advance the repository toward this goal.

Do not declare final completion.
Make a focused implementation step, then stop.
If blocked, explain the blocker clearly.
" | tee "$implementation_log"

  set +e
  bash -lc "$VERIFY_COMMAND" > "$verification_log" 2>&1
  verify_exit=$?
  set -e

  git diff > "$diff_file"

  opencode run --agent goal-evaluator "
You are an independent completion evaluator.

Original goal:

$GOAL

Verification command:

$VERIFY_COMMAND

Verification exit code:

$verify_exit

Verification output:

$(cat "$verification_log")

Git diff:

$(cat "$diff_file")

Implementation log:

$(cat "$implementation_log")

Return exactly one status on the first line:

GOAL_MET
GOAL_NOT_MET
GOAL_IMPOSSIBLE

For GOAL_NOT_MET or GOAL_IMPOSSIBLE, briefly explain why.
" | tee "$evaluation_file"

  status="$(head -n 1 "$evaluation_file" | tr -d '\r')"

  case "$status" in
    GOAL_MET)
      echo "Goal met after $i iterations."
      exit 0
      ;;
    GOAL_IMPOSSIBLE)
      echo "Goal impossible after $i iterations."
      exit 2
      ;;
    GOAL_NOT_MET)
      echo "Goal not met; continuing."
      ;;
    *)
      echo "Evaluator returned unexpected status: $status"
      exit 3
      ;;
  esac
done

echo "Goal not met after $MAX_ITERATIONS iterations."
exit 1
```

The `opencode run` and `--agent` commands in this sketch are placeholders for the actual OpenCode CLI, server API, or plugin interface selected during implementation. The durable design decision is the control boundary: the external controller, not the implementation model, decides whether to continue.

---

## Placement Guidance

Keep the conceptual specification harness-agnostic and map it to OpenCode through project configuration:

```text
.ai/
├── commands/
│   └── goal.md
├── agents/
│   └── goal-evaluator.md
├── skills/
└── runtime/
    └── goal/

.opencode/
├── commands/
│   └── goal.md
└── agents/
    └── goal-evaluator.md
```

The `.ai/` layer describes the portable methodology. The `.opencode/` layer adapts it to OpenCode primitives.

---

## OpenCode References

- [Commands](https://opencode.ai/docs/commands/) — Project-level Markdown commands and `$ARGUMENTS` substitution.
- [Agents](https://opencode.ai/docs/agents/) — Project-level agents and subagents.
- [Server](https://opencode.ai/docs/server/) — Programmatic session interaction.
- [Custom Tools](https://opencode.ai/docs/custom-tools/) — TypeScript/JavaScript tool wrappers.

---

## See Also

- [Goal Loop Pattern](../README.md) — Harness-agnostic core pattern.
- [Claude Adapter](claude.md) — Native `/goal` mapping.
- [Ralph Loop Pattern](../../ralph-loop/README.md) — Broader autonomous implementation loop.
