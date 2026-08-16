---
type: object
cluster: knowledge-types
universe: live
status: stub
entity: docs/patterns/ralph-loop/adapters/
---

# Adapter

A harness-specific or platform-specific implementation of a canonical Pattern. Adapters live inside the Pattern they implement. They must not contradict the Pattern's canonical definition — they only translate it.

## Why this shape

Patterns are harness-agnostic by design. An Adapter provides the tool-specific instructions (e.g., "in GitHub Copilot, do X") without polluting the canonical definition. This means the Pattern can be updated once and Adapters reviewed for drift, rather than maintaining N diverging copies.

## Shape

- **Harness Adapters:** `docs/patterns/<name>/adapters/<harness>.md` (e.g., `claude.md`, `copilot.md`, `opencode.md`)
- **Platform Examples:** `docs/patterns/<name>/examples/<platform>.md` (e.g., `bash.md`, `powershell.md`)
- Currently only the Ralph Loop pattern has adapters and examples

Citations: `docs/patterns/ralph-loop/adapters/`

## Connected to

- **owns:** nothing
- **owned-by:** Pattern (the Pattern folder contains its Adapters)
- **joins:** nothing
- **looks-like-but-is-not:** Skill — a Skill is an executable AI capability; an Adapter is a translation of a Pattern for a specific tool

## If you change this

- **Hits:** Nothing downstream — Adapters are leaves in the dependency graph
- **Does not hit:** The canonical Pattern definition (changing an Adapter must never alter the Pattern; if the Pattern needs updating, edit the Pattern directly)

## Surfaces

| Surface | Role |
|---|---|
| Humans | reads to run the technique in a specific tool |
| AI agents | reads when executing in a specific harness |

## See

- Source: `docs/patterns/ralph-loop/adapters/` (current only set of Adapters)
