---
type: object
cluster: workflows
universe: live
status: stub
entity: docs/patterns/feedback-loops.md
---

# Feedback Loop

A mechanism for capturing the outcome of a work unit and applying the learning to the next iteration. Operates at multiple scales: within a single implementation session, across slices in a PRD, and across handbook versions.

## Why this shape

Without deliberate outcome capture, AI-assisted work improves slowly because lessons learned in one session do not propagate to the next. The Feedback Loop is the mechanism that turns experience into encoded practice — and eventually into new Patterns and Playbooks.

## Shape

- Defined in `docs/patterns/feedback-loops.md`
- Applied at the end of each Vertical Slice and each delivery cycle
- Distinct from the Ralph Loop's per-iteration feedback (which is about test results, not process learning)

Citations: `docs/patterns/feedback-loops.md`

## Connected to

- **owns:** nothing
- **owned-by:** Validation and Resolution phases of the Software Development Process
- **joins:** RalphLoop (the Loop's blocker/complete output feeds into the broader Feedback Loop); `system/authoring/` (knowledge learned through feedback is integrated into the handbook via the authoring process)
- **looks-like-but-is-not:** RalphLoop — Ralph Loop is the execution cycle; Feedback Loop is the learning mechanism that operates above it

## If you change this

- **Hits:** `docs/processes/software-development.md` Validation/Resolution phases; `docs/references/cross-links.md`
- **Does not hit:** RalphLoop definition (the loop does not depend on the broader feedback mechanism)

## Surfaces

| Surface | Role |
|---|---|
| Humans | primary actor — captures what was learned and decides what to codify |
| AI agents | reads to understand how to summarize and report session outcomes |

## See

- Source: `docs/patterns/feedback-loops.md`
