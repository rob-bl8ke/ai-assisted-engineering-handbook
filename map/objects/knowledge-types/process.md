---
type: object
cluster: knowledge-types
universe: live
status: stub
entity: docs/processes/software-development.md
---

# Process

An end-to-end workflow in `docs/processes/` that orchestrates multiple phases, each of which may invoke Playbooks, Patterns, and Skills. Currently one Process exists: the Software Development Process.

## Why this shape

A Process is the highest-level sequencing artifact. It answers "what phase am I in and what do I do next?" for the full lifecycle. It deliberately does not contain step-by-step instructions — those live in Playbooks. The Process just names the phases and points at the right Playbooks and Skills for each.

## Shape

- Single file `docs/processes/<name>.md`
- Contains: phase overview diagram, per-phase goals, human judgment points, links to Playbooks and Skills per phase
- The Software Development Process has 6 phases: Discovery → Design → Decomposition → Implementation → Validation → Resolution

Citations: `docs/processes/software-development.md:1–60`

## Connected to

- **owns:** nothing (orchestrates; does not own)
- **owned-by:** README.md decision tree mirrors the Process phases
- **joins:** Playbooks (each phase invokes specific Playbooks); Skills (phases invoke Skills at decision points)
- **looks-like-but-is-not:** Authoring workflow (`system/authoring/`) — that is the Process for maintaining the *handbook itself*, not for software delivery

## If you change this

- **Hits:** `README.md` decision tree (must stay in sync with phase names and order); any Playbook referenced from a phase; `docs/references/cross-links.md`
- **Does not hit:** `system/authoring/` (independent workflow); Patterns (Process references but does not own them)

## Surfaces

| Surface | Role |
|---|---|
| Humans | reads to orient in the delivery lifecycle |
| AI agents | reads to determine which phase-appropriate Skill to invoke |
| `README.md` | mirrors phase structure in the decision tree |

## See

- Source: `docs/processes/software-development.md`
