---
type: object
cluster: knowledge-types
universe: live
status: stub
entity: docs/skills/README.md
---

# Skill

A two-part artifact: `docs/skills/<name>.md` is the canonical spec (what the skill does, harness-agnostic); `.agents/skills/<name>/SKILL.md` is the executable (how an agent runs it in a specific harness).

## Why this shape

Separating spec from executable means the canonical behaviour can be read and validated by a human without loading any agent tooling, and the same spec can back multiple harness implementations. Changing one without the other causes drift.

## Shape

- **Spec:** `docs/skills/<name>.md` — phases, decision points, output checklist, maturity evidence
- **Executable:** `.agents/skills/<name>/SKILL.md` — runnable instructions for a specific harness
- **Agents config (optional):** `.agents/skills/<name>/agents/*.yaml`

Citations: `docs/skills/README.md`

## Connected to

- **owns:** executable in `.agents/skills/<name>/`
- **owned-by:** Playbook (a Skill wraps a mature Playbook)
- **joins:** Process (processes invoke Skills at specific phases)
- **looks-like-but-is-not:** Playbook — a Playbook is guidance for humans; a Skill is runnable by an agent

## If you change this

- **Hits:** `.agents/skills/<name>/SKILL.md` must reflect the updated behaviour; `docs/references/cross-links.md` if the skill's phases change
- **Does not hit:** Other skills; Pattern cards (unless the skill wraps a pattern directly)

## Surfaces

| Surface | Role |
|---|---|
| AI agents | reads executable (`.agents/skills/`) |
| Humans | reads spec (`docs/skills/`) |
| `skills-lock.json` | tracks installed skill versions |

## See

- Source: `docs/skills/README.md`
- Maturity criteria: `docs/skills/skill-maturity-criteria.md`
