---
type: object
cluster: workflows
universe: live
status: stub
entity: docs/patterns/context-management.md
---

# Context Management

The discipline of deliberately scoping the information given to an AI agent so it matches the task at hand — no more, no less.

## Why this shape

AI models have a fixed context window. Loading the entire codebase or entire handbook into every request produces worse output (signal buried in noise), slower response, and higher cost. Context Management is the practice of loading only what a step needs — the ICM principle of "load only what the step needs" operationalized as a technique.

## Shape

- Defined in `docs/patterns/context-management.md`
- Applied as a cross-cutting technique in every other pattern and playbook
- No harness adapters — it is a meta-technique about how to structure prompts and file reads

Citations: `docs/patterns/context-management.md`

## Connected to

- **owns:** nothing
- **owned-by:** nothing (cross-cutting)
- **joins:** every Skill and Playbook (all should apply context management when specifying what to read); RalphLoop (the loop's per-iteration context budget is a Context Management decision); `map/` itself (this map is an application of Context Management)
- **looks-like-but-is-not:** nothing unique; it is the enabling principle behind ICM's "token discipline"

## If you change this

- **Hits:** `docs/references/cross-links.md`; potentially every Skill spec that defines what to load
- **Does not hit:** Source files being referenced (Context Management is about how to read them, not what they say)

## Surfaces

| Surface | Role |
|---|---|
| Humans | applies when writing Skill specs and Playbooks |
| AI agents | applies when deciding which files to read for a task |

## See

- Source: `docs/patterns/context-management.md`
