---
type: process
status: stub
consumes:
  - map/objects/knowledge-types/playbook.md
produces:
  - map/objects/knowledge-types/skill.md
---

# skill-ify

Promote a mature Playbook to an executable Skill by verifying it meets the maturity criteria, writing the canonical spec, and creating a harness-specific executable.

## Input → Movement → Output

**Input:** A Playbook in `docs/playbooks/` that has been followed multiple times and has consistent, verifiable outcomes. **Movement:** Evaluate against the four maturity criteria → Write the Skill spec in `docs/skills/` → Create the executable in `.agents/skills/` → Link spec to executable. **Output:** A new Skill with two files: canonical spec and harness-specific executable, both referencing the source Playbook.

## Why this shape

Promoting too early (before consistent outcomes) creates Skills that are brittle or wrong. The maturity gate exists to keep Skills trustworthy — an agent invoking a Skill must be able to trust that it encodes real, validated practice.

## Steps

1. **Evaluate** — Score the Playbook against all four criteria in `docs/skills/skill-maturity-criteria.md`. All four must pass. 🟡 Human gate: criteria met?
2. **Write spec** — Create `docs/skills/<name>.md`. Must be harness-agnostic. Include: phases, decision points, output checklist, evidence of consistent outcomes.
3. **Write executable** — Create `.agents/skills/<name>/SKILL.md`. References the spec. Harness-specific instructions go here, not in the spec.
4. **Cross-link** — Update `docs/references/cross-links.md`. Update `docs/skills/README.md` index. Add stub card to `map/objects/knowledge-types/` (or update existing skill card).
5. **Register** — Add to `skills-lock.json` if this is an installable external skill.

Citations: `docs/skills/skill-maturity-criteria.md`

## If you change this

- **Hits:** `docs/skills/skill-maturity-criteria.md` (the criteria are the gate — if this process changes, verify the criteria still align); `docs/skills/README.md`
- **Does not hit:** Existing Skills that have already been promoted

## Surfaces

| Surface | Role |
|---|---|
| Humans | evaluates maturity criteria; approves promotion |
| AI agents | can draft spec and executable; human validates before merge |

## See

- Objects: [Skill](../objects/knowledge-types/skill.md), [Playbook](../objects/knowledge-types/playbook.md)
- Source: `docs/skills/skill-maturity-criteria.md`
