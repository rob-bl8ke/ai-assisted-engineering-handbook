---
type: process
status: stub
consumes:
  - map/objects/workflows/prd.md
  - map/objects/workflows/vertical-slice.md
  - map/objects/workflows/ralph-loop.md
produces:
  - shipped code
  - updated feedback records
---

# deliver

Move a problem from initial concept through working, validated, deployed software using the AI-assisted software development lifecycle.

## Input → Movement → Output

**Input:** A problem statement, feature idea, bug report, or technical debt item. **Movement:** Six sequential phases — Discovery, Design, Decomposition, Implementation, Validation, Resolution — each ending at a human gate. **Output:** Shipped code and a feedback record that may produce new Patterns or Playbooks.

## Why this shape

Phases separate concerns that fail when mixed: requirements written during implementation are untestable; code reviewed before decomposition is scoped wrong. The human gates between phases are the error-correction mechanism.

## Steps

1. **Discovery** — Establish shared understanding. Run the Grill Me skill if ambiguity exists. `docs/skills/grill-me.md` | 🟡 Human gate: does this warrant a PRD?
2. **Design** — Create a PRD. Use Write PRD skill. `docs/skills/write-prd.md` | 🟡 Human gate: PRD approved?
3. **Decomposition** — Break PRD into Vertical Slices as Issues. Use PRD to Issues skill. `docs/skills/prd-to-issues.md` | 🟡 Human gate: issues reviewed and sized?
4. **Implementation** — Execute each Issue. Use Ralph Loop for autonomous execution or supervised implementation. `docs/patterns/ralph-loop/README.md`
5. **Validation** — Review implementation. Use Review Implementation playbook. `docs/playbooks/review-implementation.md` | 🟡 Human gate: review approved?
6. **Resolution** — Integrate, deploy, plan follow-on work. Capture outcomes as Feedback Loop input.

Citations: `docs/processes/software-development.md`

## If you change this

- **Hits:** `README.md` decision tree (must mirror phase names and order); all phase-linked Skills and Playbooks
- **Does not hit:** `system/authoring/` (independent workflow); Patterns (referenced, not owned)

## Surfaces

| Surface | Role |
|---|---|
| Humans | follows phases; approves at gates |
| AI agents | reads to determine current phase and appropriate Skill to invoke |
| `README.md` | mirrors this process as the entry-point decision tree |

## See

- Objects: [PRD](../objects/workflows/prd.md), [VerticalSlice](../objects/workflows/vertical-slice.md), [RalphLoop](../objects/workflows/ralph-loop.md)
- Source: `docs/processes/software-development.md`
