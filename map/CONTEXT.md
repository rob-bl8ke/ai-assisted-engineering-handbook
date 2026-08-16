# Map — CONTEXT

How to walk this map, what universes exist, and which names collide.

## Universes

| Universe | Meaning |
|---|---|
| **live** | In force. All content is currently live — this repo has no deprecated paths. |
| **leftover** | Still present, no longer primary. None currently. |
| **ghost** | Named or filed, not wired (stubs, dead references). None currently. |

## How to walk

1. Open `map/CLAUDE.md`. Find the routing table row that matches your question.
2. Follow the link to the relevant card or process file.
3. The card names its source file — open that for the authoritative content.
4. If you are changing something, read the card's **If you change this** section before touching the source.

Token budget per task: entry file + one card + its source file ≈ 2k–6k tokens. Do not load the full tree.

## Name collisions

Fully resolved in `CLAUDE.md` routing table. Summary:

- **Skill** has two locations: `docs/skills/` (spec, what-to-do) and `.agents/skills/` (executable, how-to-run). They must stay in sync. Changing the spec without updating the executable leaves the agent running stale behaviour.
- **Process** names the delivery lifecycle AND the authoring workflow. They are independent. Changing one does not hit the other.
- **Tracer Bullet** and **Vertical Slice** look similar. A tracer bullet is an _implementation technique_ (build the thinnest path first). A vertical slice is a _decomposition unit_ (end-to-end scope of a work item). A slice may use a tracer bullet internally.

## Map status

Cards status is tracked by frontmatter `status:` field:

| Status | Meaning |
|---|---|
| `stub` | Shape known, body not yet verified against source |
| `verified` | Body verified against source on the stated date |
| `stale` | Verification date is old; re-verify before citing |

Re-verify any `verified` card that is more than 3 months old before making a change that cites its **Hits** section.

## Walk test checklist

Run after any structural change to the map:

- [ ] Root entry file answers *where am I* and *where do I go* within two reads?
- [ ] Each card cites a source file path
- [ ] Each card's **If you change this → Hits** names at least one downstream noun
- [ ] No fact lives in two cards (link instead)
- [ ] `objects/_index.md` has a row for every noun that has a card
- [ ] `map/AGENTS.md` and `map/routing.md` are byte-identical to `map/CLAUDE.md`
