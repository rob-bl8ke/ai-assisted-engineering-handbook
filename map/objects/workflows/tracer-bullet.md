---
type: object
cluster: workflows
universe: live
status: stub
entity: docs/patterns/tracer-bullets.md
---

# Tracer Bullet

A minimal end-to-end implementation that proves the approach works before investing in the full solution. The thinnest path through all system layers that produces observable output.

## Why this shape

A tracer bullet surfaces integration risk early. It is not a prototype to be thrown away — it is production-quality code on the skeleton, validated at every layer, that subsequent work fills in. It collapses the "will this actually work?" question from the end of a Vertical Slice to the beginning.

## Shape

- Defined in `docs/patterns/tracer-bullets.md`
- Applied within a Vertical Slice as the first implementation pass
- Does not have its own harness adapters — it is a technique described abstractly

Citations: `docs/patterns/tracer-bullets.md`

## Connected to

- **owns:** nothing
- **owned-by:** nothing (a technique applied within Slices)
- **joins:** VerticalSlice (used as the opening move of a slice); TDD (tracer bullets are typically test-driven)
- **looks-like-but-is-not:** Vertical Slice — a Slice is the *scope*; a Tracer Bullet is the *first implementation pass* within that scope

## If you change this

- **Hits:** `docs/references/cross-links.md`; any Playbook that references this pattern
- **Does not hit:** VerticalSlice definition (the slice concept does not depend on how it is implemented)

## Surfaces

| Surface | Role |
|---|---|
| Humans | applies the technique when starting a new Slice |
| AI agents | reads when given "implement a tracer bullet" instruction |

## See

- Source: `docs/patterns/tracer-bullets.md`
