# AI-Assisted Engineering Handbook — System Map

A walkable graph of every durable concept in this repo: what each noun is, how verbs move, and what else moves when you change something.

The **subject repo** is at the root. This map cites it. The map is never a second spec.

## Where things live

| Folder | What it holds |
|---|---|
| `map/objects/knowledge-types/` | The six artifact kinds: Skill, Pattern, Playbook, Process, Reference, Adapter |
| `map/objects/workflows/` | The six core methods: RalphLoop, VerticalSlice, PRD, TracerBullet, FeedbackLoop, ContextManagement |
| `map/processes/` | Three repeating verbs: deliver, author, skill-ify |
| `map/_meta/schema.md` | Closed node types and naming rules |
| `map/_templates/` | Copyable starters for new cards |
| `map/objects/_index.md` | One-line status for every noun |

## Route by question

| Question | Go to | Stop at |
|---|---|---|
| What kind of file do I add / where does it go? | `map/objects/knowledge-types/` | the matching card |
| What is X and what else moves if I change it? | `map/objects/workflows/` or `_index.md` | the matching card |
| How does the end-to-end delivery flow work? | `map/processes/deliver.md` | — |
| How do I add new knowledge to the handbook? | `map/processes/author.md` | — |
| When should I promote a playbook to a skill? | `map/processes/skill-ify.md` | — |
| What names collide or alias? | `map/CONTEXT.md` | name-collision table |

## Name collisions (resolve before writing)

| Word used | Means here | Does NOT mean |
|---|---|---|
| **Skill (spec)** | `docs/skills/*.md` — canonical behaviour definition | `.agents/skills/*/SKILL.md` (that is the executable) |
| **Skill (executable)** | `.agents/skills/*/SKILL.md` — harness-runnable file | `docs/skills/*.md` (that is the spec) |
| **Process** | `docs/processes/software-development.md` — delivery lifecycle | `system/authoring/` workflow (that is the authoring process) |
| **Tracer Bullet** | Implementation probe technique (`docs/patterns/tracer-bullets.md`) | Vertical Slice (related but distinct; see cards) |

## The one rule

Every change must satisfy the walk test in `map/CONTEXT.md` before it is merged.
