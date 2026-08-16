---
type: object
cluster: knowledge-types
universe: live
status: stub
entity: docs/patterns/
---

# Pattern

A reusable technique defined once in `docs/patterns/` and referenced by Playbooks, Processes, and Skills — never duplicated into them.

## Why this shape

Defining a technique in one place means every Playbook that uses it stays current when the technique evolves. A Pattern is the stable claim; Adapters and Examples are its harness-specific expressions.

## Shape

- **Simple pattern:** single file `docs/patterns/<name>.md`
- **Complex pattern with adapters:** folder `docs/patterns/<name>/README.md` + `adapters/` + `examples/`

The `README.md` of a complex pattern is the canonical definition. Adapters must not contradict it.

## Connected to

- **owns:** Adapter files (`docs/patterns/<name>/adapters/`), Example files (`docs/patterns/<name>/examples/`)
- **owned-by:** Process (processes invoke patterns at phases); Playbook (playbooks operationalize patterns)
- **joins:** Skill (mature patterns underlie Skills)
- **looks-like-but-is-not:** Playbook — Pattern = the *what* (the technique); Playbook = the *how* (the step-by-step procedure)

## If you change this

- **Hits:** All Adapters and Examples for this pattern; any Playbook that references it; `docs/references/cross-links.md`
- **Does not hit:** Other patterns (patterns are independent unless explicitly linked)

## Surfaces

| Surface | Role |
|---|---|
| Humans | reads `docs/patterns/` |
| AI agents | reads via Playbook or Skill instructions |
| `docs/references/cross-links.md` | indexes pattern relationships |

## See

- Source: `docs/patterns/`
