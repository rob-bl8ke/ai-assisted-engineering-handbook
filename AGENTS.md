# Agent Maintenance Guide: AI-Assisted Engineering Handbook

**Purpose**: Maintain this living knowledge base as an interconnected system. Prevent duplication, keep vocabulary consistent, respect layer structure, and ensure clear navigation.

## What This Is

A GitHub-hosted handbook answering: **Given my engineering situation, what AI-assisted approach should I follow, why, and how do I execute it?**

## Structure

```
docs/
├── processes/ — End-to-end workflows
├── patterns/ — Reusable solutions (vertical-slicing, ralph-loop, etc.)
│   └── ralph-loop/ (canonical + adapters: claude/copilot/opencode + examples: bash/powershell)
├── playbooks/ — Step-by-step guides
├── skills/ — Executable capabilities
├── agents/ — Domain modeling, issue tracking
└── references/ — Provenance, cross-links, MVP verification
system/authoring/ — Knowledge integration process (classify → search → integrate → cross-link → validate)
```

## Vocabulary

- **Process** — Structured workflow for major engineering activity
- **Pattern** — Reusable solution to recurring challenge
- **Principle** — Core idea underlying effective AI-assisted engineering
- **Playbook** — Step-by-step guide combining principles and patterns
- **Skill** — Executable AI capability
- **Adapter** — Harness-specific implementation (Claude, Copilot, OpenCode)
- **Example** — Platform-specific demonstration (Bash, PowerShell)

## Core Principles

1. **Canonical Knowledge is Harness-Agnostic** — Separate conceptual approaches from tool-specific implementations. One pattern → multiple adapters (Claude, Copilot, OpenCode) and examples (Bash, PowerShell).

2. **No Duplication** — If a concept exists, reference it. Use cross-links to maintain coherence.

3. **Structured Layers** — Navigation layer (decision trees) → Knowledge layer (patterns/playbooks) → Execution layer (skills) → Adapter layer (tool-specific).

4. **Clear Navigation** — Every major document has title, purpose, and related links to enable discovery.

## Common Maintenance Tasks

**Add New Skill/Technique**: Determine layer → Check for duplicates → Create or augment → Cross-link → Update navigation

**Integrate External Knowledge**: When a human provides knowledge to integrate (article, code, experience, technique, etc.), you **must** follow these steps in order — do not skip any:

1. **Classify** — Determine the type (Pattern, Playbook, Adapter, Example, Skill, Principle?) and the correct structural location. See [system/authoring/classify.md](system/authoring/classify.md).
2. **Search** — Read existing related docs to check whether this knowledge already exists or overlaps. Do not integrate before searching.
3. **Integrate** — Update existing documents where appropriate. Create new content only if no suitable home exists. Prefer enriching over creating.
4. **Cross-link** — Connect the new/updated content to related processes, patterns, and playbooks. Update [docs/references/cross-links.md](docs/references/cross-links.md).
5. **Validate** — Verify structure, navigation, vocabulary consistency, and that no links are broken. See [system/authoring/validate.md](system/authoring/validate.md).

**Update Process/Pattern**: Preserve compatibility → Document change → Update cross-links → Validate navigation

**Handle Duplicates**: Identify overlap → Keep authoritative version → Reference from duplicates → Update all cross-links

**Record Provenance**: Use format in [docs/references/provenance.md](docs/references/provenance.md); track changes via git commits and issues

## Where to Find Things

- **Understand the workflow**: [docs/processes/software-development.md](docs/processes/software-development.md)
- **Add new capability**: [system/authoring/README.md](system/authoring/README.md)
- **Classify knowledge**: [system/authoring/classify.md](system/authoring/classify.md)
- **Understand a pattern**: `docs/patterns/` → check adapters in subdirectories
- **Understand a skill**: `docs/skills/` → see [skill-maturity-criteria.md](docs/skills/skill-maturity-criteria.md)
- **Understand playbooks**: [docs/playbooks/](docs/playbooks/) for step-by-step guides
- **Check MVP status**: [docs/references/MVP-VERIFICATION.md](docs/references/MVP-VERIFICATION.md)
- **Record provenance**: [docs/references/provenance.md](docs/references/provenance.md)
- **Understand cross-linking**: [docs/references/cross-links.md](docs/references/cross-links.md)

## Critical Constraints

1. **Do NOT define concepts multiple places** — Canonical knowledge has one home; others reference it
2. **Do NOT mix tool-agnostic and tool-specific guidance** — Pattern → adapters for different tools
3. **Do NOT leave orphaned content** — Link every new file/section from its parent and related content
4. **Do NOT change process definitions without documentation** — Include rationale, update dependents
5. **Do NOT break existing cross-links** — Verify references before moving/deleting files
6. **Do NOT duplicate examples** — One canonical example per tool/platform per pattern

## Maintenance Checklist

- [ ] Vocabulary consistent with definitions
- [ ] No duplicate concepts (checked existing docs)
- [ ] All new content linked from appropriate parents
- [ ] Navigation clear (discoverable from related content)
- [ ] Provenance recorded if integrating external knowledge
- [ ] Harness-agnostic and adapter layers properly separated
- [ ] Cross-links updated in [docs/references/cross-links.md](docs/references/cross-links.md)
- [ ] No broken internal links

## Your Role as an Agent Maintainer

You are responsible for:

1. **Coherence**: Keeping knowledge organized, preventing duplication, maintaining vocabulary
2. **Navigation**: Ensuring humans and agents can find what they need
3. **Structure**: Respecting layers (canonical → adapters → examples)
4. **Attribution**: Recording sources and changes via provenance
5. **Growth**: Integrating new knowledge while maintaining these principles

---

**Last Updated**: 2026-08-16
**Maintained By**: AI Agents (with human review)
