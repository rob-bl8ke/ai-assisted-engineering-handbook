# Schema — map node types

The closed set of node types, their locations, and naming rules. When practice and this file disagree, fix the file or fix the practice — pick one, same day.

## Node types

| `type:` | Lives at | Carries |
|---|---|---|
| `object` | `map/objects/<cluster>/<slug>.md` | shape, connected-to, if-you-change-this, surfaces, source |
| `process` | `map/processes/<slug>.md` | input→movement→output, steps with citations, hits |
| `index` | `map/objects/_index.md` | one line per noun: slug, status, one-sentence summary |

## Clusters

| Cluster | Slug prefix | Purpose |
|---|---|---|
| `knowledge-types` | noun | The six artifact kinds (Skill, Pattern, Playbook, Process, Reference, Adapter) |
| `workflows` | noun | The six core methods (RalphLoop, VerticalSlice, PRD, TracerBullet, FeedbackLoop, ContextManagement) |

## Required frontmatter

```yaml
---
type: object          # or process
cluster: knowledge-types | workflows   # objects only
universe: live        # live | leftover | ghost
status: stub          # stub | verified | stale
verified_date:        # ISO date, only when status: verified
entity: path/to/owning/file.md
---
```

## Naming

- Filenames: `kebab-case.md`
- Frontmatter slugs match filename (no spaces)
- Do not hand-edit `objects/_index.md` — regenerate it by reading each card's first line and frontmatter
