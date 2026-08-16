---
type: object
cluster: knowledge-types
universe: live
status: stub
entity: docs/references/
---

# Reference

Supporting documentation in `docs/references/` covering two concerns: **provenance** (where ideas in this handbook originated) and **cross-links** (the concept-relationship graph that connects all handbook artifacts).

## Why this shape

Provenance records the intellectual debt behind each technique so the handbook can be updated when source material evolves and so readers can verify claims. Cross-links prevent conceptual drift between artifacts that reference the same underlying idea.

## Shape

- `docs/references/provenance.md` — how to attribute ideas; which sources are load-bearing
- `docs/references/cross-links.md` — concept relationship map; lists links between artifacts
- `docs/references/README.md` — explains why provenance matters in this handbook

Citations: `docs/references/README.md`

## Connected to

- **owns:** nothing
- **owned-by:** nothing (cross-cutting concern)
- **joins:** every artifact in `docs/` (each should have a provenance entry if it derives from external work)
- **looks-like-but-is-not:** `map/` (this map is structural navigation; References are intellectual attribution)

## If you change this

- **Hits:** Any artifact whose provenance entry changes must be reviewed for factual accuracy
- **Does not hit:** `system/authoring/` (authoring process is independent of provenance records)

## Surfaces

| Surface | Role |
|---|---|
| Humans | reads to verify attribution and find related concepts |
| AI agents | reads `cross-links.md` to understand concept relationships before editing |

## See

- Source: `docs/references/`
