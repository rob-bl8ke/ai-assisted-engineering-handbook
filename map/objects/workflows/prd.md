---
type: object
cluster: workflows
universe: live
status: stub
entity: docs/playbooks/create-prd.md
---

# PRD (Product Requirements Document)

The formal specification artifact produced during the Design phase that bridges Discovery (shared understanding) and Decomposition (implementation issues). A PRD answers: what should be built, why, and how do we know when it is done.

## Why this shape

Without a PRD, decomposition is guesswork. The PRD is the contract between the problem statement and the implementation plan. It is the edit surface that a human reviews and approves before any code is written.

## Shape

- Created via: `docs/playbooks/create-prd.md` (human-guided) or `docs/skills/write-prd.md` (AI-assisted)
- Contains: vision, problem statement, success criteria, acceptance criteria, constraints
- Lives in the target repo or issue tracker (not in this handbook)

Citations: `docs/playbooks/create-prd.md`, `docs/skills/write-prd.md`

## Connected to

- **owns:** Vertical Slices (a PRD is decomposed into slices by PRD to Issues)
- **owned-by:** Discovery phase output (the PRD formalizes what Discovery uncovered)
- **joins:** `docs/skills/prd-to-issues.md` (skill that reads a PRD and produces issues)
- **looks-like-but-is-not:** GitHub Issue (an Issue is one Vertical Slice; a PRD is the full specification)

## If you change this

- **Hits:** `docs/skills/prd-to-issues.md` (if PRD structure changes, the decomposition skill must adapt); `docs/playbooks/decompose-work.md`
- **Does not hit:** `docs/skills/grill-me.md` (Grill Me feeds into PRD creation but does not depend on PRD structure)

## Surfaces

| Surface | Role |
|---|---|
| Humans | authors and approves; primary human gate |
| AI agents | reads to decompose into issues; reads via `write-prd.md` skill to draft |

## See

- Source: `docs/playbooks/create-prd.md`
- Skill: `docs/skills/write-prd.md`
