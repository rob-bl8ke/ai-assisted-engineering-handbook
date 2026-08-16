---
type: process
status: stub
consumes:
  - new knowledge (idea, technique, source material)
produces:
  - updated docs/ artifact
  - updated cross-links
---

# author

Add new knowledge to, or update existing knowledge in, the handbook using the four-phase authoring workflow.

## Input → Movement → Output

**Input:** A technique, idea, pattern, playbook, or reference sourced externally or discovered through delivery work. **Movement:** Classify the knowledge type → Integrate it at the right level → Cross-link it to related artifacts → Validate the resulting structure. **Output:** A new or updated handbook artifact with provenance recorded and cross-links updated.

## Why this shape

Classifying before writing prevents the wrong artifact type being created (e.g., a Pattern written as a Playbook, or a one-off procedure elevated to a Process). Validating after writing catches structural drift before it accumulates.

## Steps

1. **Classify** — Determine the knowledge type (Pattern, Playbook, Process, Skill, Reference). `system/authoring/classify.md`
2. **Integrate** — Write or update the artifact at the correct path. Follow naming and frontmatter rules in `map/_meta/schema.md`. `system/authoring/integrate.md`
3. **Cross-link** — Update `docs/references/cross-links.md` with new relationships. `docs/references/cross-links.md`
4. **Validate** — Run `system/authoring/validate.md` checklist. Check that the walk test in `map/CONTEXT.md` still passes.
5. **Update the map** — If a new noun was added, add a stub card in `map/objects/<cluster>/` and a row in `map/objects/_index.md`. 🟡 Human gate: is the map still coherent?

Citations: `system/authoring/README.md`

## If you change this

- **Hits:** Every artifact that gets authored flows through this process; changing its phases changes the quality gate for all handbook content
- **Does not hit:** `docs/processes/software-development.md` (delivery process is independent)

## Surfaces

| Surface | Role |
|---|---|
| Humans | decides classification; approves integration |
| AI agents | can run steps 1–3 autonomously; step 4–5 require human review |

## See

- Objects: [Skill](../objects/knowledge-types/skill.md), [Pattern](../objects/knowledge-types/pattern.md), [Playbook](../objects/knowledge-types/playbook.md)
- Source: `system/authoring/README.md`
