---
type: object
cluster: knowledge-types
universe: live
status: stub
entity: docs/playbooks/
---

# Playbook

A step-by-step procedure in `docs/playbooks/` that operationalizes one or more Patterns for a specific engineering scenario. A Playbook answers "how do I do X right now?" A mature Playbook becomes a Skill.

## Why this shape

Procedures that are too specific or too early-stage to be automated live here. They keep human judgment in the loop while still encoding repeatable structure. Once a Playbook accumulates evidence of consistent outcomes and automatable decision points, it graduates to a Skill.

## Shape

- Single file `docs/playbooks/<name>.md`
- Contains: phases, decision points marked "🟡 Human Judgement Point", step-by-step actions, output checklist

Citations: `docs/skills/skill-maturity-criteria.md`

## Connected to

- **owns:** nothing (Playbooks cite Patterns; they do not own them)
- **owned-by:** Process (phases of a Process invoke Playbooks)
- **joins:** Skill (a mature Playbook is wrapped by a Skill)
- **looks-like-but-is-not:** Pattern — Pattern = reusable technique; Playbook = sequenced procedure for a scenario

## If you change this

- **Hits:** Any Skill that wraps this Playbook (check `docs/skills/` for references); `docs/references/cross-links.md`
- **Does not hit:** Patterns the Playbook references (Playbooks consume Patterns; changing the Playbook does not change the Pattern)

## Surfaces

| Surface | Role |
|---|---|
| Humans | primary audience; reads and follows steps |
| AI agents | reads as supporting context when executing a Skill |

## See

- Source: `docs/playbooks/`
- Promotion criteria: `docs/skills/skill-maturity-criteria.md`
