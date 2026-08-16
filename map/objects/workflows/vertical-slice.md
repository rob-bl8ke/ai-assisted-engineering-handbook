---
type: object
cluster: workflows
universe: live
status: stub
entity: docs/patterns/vertical-slicing.md
---

# Vertical Slice

A decomposition unit: an end-to-end work item that spans all system layers (UI to data), can be independently tested and deployed, and is scoped to approximately 1–3 days of focused work.

## Why this shape

Slices that cut horizontally (all UI, then all backend) cannot be validated until the last layer is done. A vertical cut produces something testable at every stage, enables early feedback, and limits the blast radius of a mis-scoped requirement.

## Shape

- Defined in `docs/patterns/vertical-slicing.md`
- Produced by the Decompose Work Playbook (`docs/playbooks/decompose-work.md`)
- Operationalized by the PRD to Issues Skill (`docs/skills/prd-to-issues.md`)
- A Slice maps to one GitHub Issue in this repo's workflow

Citations: `docs/patterns/vertical-slicing.md`

## Connected to

- **owns:** nothing (a slice is a work unit, not a container)
- **owned-by:** PRD (a PRD is decomposed into Slices)
- **joins:** TracerBullet (first implementation of a slice often uses a tracer bullet); RalphLoop (the loop runs on a slice)
- **looks-like-but-is-not:** TracerBullet — a Slice is the *scope of work*; a Tracer Bullet is an *implementation technique* used within a slice

## If you change this

- **Hits:** `docs/playbooks/decompose-work.md`; `docs/skills/prd-to-issues.md`; `docs/references/cross-links.md`
- **Does not hit:** RalphLoop definition (the loop is agnostic to how slices are defined)

## Surfaces

| Surface | Role |
|---|---|
| Humans | sizes and validates slices during Decomposition phase |
| AI agents | reads pattern when decomposing a PRD into issues |

## See

- Source: `docs/patterns/vertical-slicing.md`
