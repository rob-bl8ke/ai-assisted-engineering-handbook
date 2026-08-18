# Domain Docs

How engineering skills should consume and maintain a repo's domain documentation when exploring the codebase.

These conventions support [Grill With Docs](../skills/grill-with-docs.md), which combines [Grill Me](../skills/grill-me.md) with lightweight Domain-Driven Design. The goal is a shared ubiquitous language that both humans and agents use consistently.

## Before exploring, read these

- **`CONTEXT.md`** at the repo root, or
- **`CONTEXT-MAP.md`** at the repo root if it exists -- it points at one `CONTEXT.md` per context. Read each one relevant to the topic.
- **`docs/adr/`** -- read ADRs that touch the area you're about to work in. In multi-context repos, also check `src/<context>/docs/adr/` for context-scoped decisions.

If any of these files don't exist, **proceed silently**. Don't flag their absence; don't suggest creating them upfront. The `/domain-modeling` skill (reached via `/grill-with-docs` and `/improve-codebase-architecture`) creates them lazily when terms or decisions actually get resolved.

## File structure

Single-context repo (most repos):

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

Multi-context repo (presence of `CONTEXT-MAP.md` at the root):

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          <- system-wide decisions
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  <- context-specific decisions
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in `CONTEXT.md`. Don't drift to synonyms the glossary explicitly avoids.

If the concept you need isn't in the glossary yet, that's a signal -- either you're inventing language the project doesn't use (reconsider) or there's a real gap (note it for `/domain-modeling`).

## Maintain docs only when knowledge crystallizes

During grilling, update domain docs when the conversation resolves a term or a durable decision:

- Add or refine `CONTEXT.md` entries when domain terms become precise.
- Create or update ADRs only for decisions that are hard to reverse, surprising without context, and the result of a real trade-off.
- Keep `CONTEXT.md` implementation-free; it is a glossary, not a specification or scratch pad.
- Separate facts discovered from code/docs from decisions made by the user.

## Flag ADR conflicts

If your output contradicts an existing ADR, surface it explicitly rather than silently overriding:

> _Contradicts ADR-0007 (event-sourced orders) -- but worth reopening because..._

## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Domain docs during grilling | Matt Pocock `domain-modeling` skill | Tool/Repository | Active glossary and ADR discipline during engineering design conversations | https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md |
| Skill composition | Matt Pocock `grill-with-docs` skill | Tool/Repository | Combines `grilling` and `domain-modeling` | https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md |
