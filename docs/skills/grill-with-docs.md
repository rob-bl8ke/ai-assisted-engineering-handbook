# Skill: Grill With Docs

## Overview

**Grill With Docs** is the software-engineering variant of [Grill Me](grill-me.md). It combines grilling with domain modeling so that shared understanding becomes durable project knowledge, not just a conversation summary.

**Canonical Purpose:** Stress-test a plan or design while capturing the ubiquitous language, glossary terms, and durable architectural decisions that future agents and humans must use.

**Related Skills and References:**
- [Grill Me](grill-me.md) - base grilling behavior and confirmation discipline
- [Domain Docs](../agents/domain.md) - handbook conventions for `CONTEXT.md`, `CONTEXT-MAP.md`, and ADRs
- [Matt Pocock: grill-with-docs](https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md) - source skill that composes `grilling` and `domain-modeling`
- [Matt Pocock: domain-modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md) - source DDD-oriented documentation skill

---

## Canonical Skill Behavior

Use [Grill Me](grill-me.md) for the conversation mechanics:
- Build and refine a design tree
- Ask one question at a time
- Discover facts through codebase and documentation exploration
- Ask the user for decisions, not discoverable facts
- Summarize shared understanding before acting
- Wait for explicit confirmation before implementation

Add domain-modeling behavior when the work touches business concepts, bounded contexts, long-lived architecture, or terminology that humans and agents will reuse.

### Domain Modeling Discipline

- Read existing domain docs before proposing new language: root `CONTEXT.md`, `CONTEXT-MAP.md` if present, and relevant ADRs.
- Challenge fuzzy or overloaded terms immediately.
- Prefer the project glossary's vocabulary over invented synonyms.
- Use concrete scenarios and edge cases to clarify boundaries between concepts.
- Cross-check user claims against code when the repository can answer the question.
- Update domain documentation only when a term or decision has crystallized.
- Create docs lazily; do not create `CONTEXT.md` or ADR files before there is resolved knowledge to capture.

### What To Capture

Capture glossary terms in `CONTEXT.md` when the team resolves domain language:
- Canonical term
- Meaning in the domain
- Terms to avoid or distinguish
- Important examples and non-examples

Capture ADRs only when the decision is:
- Hard to reverse
- Surprising without context
- The result of a real trade-off

Do not put implementation plans, scratch notes, or unresolved speculation in `CONTEXT.md`. It is a domain glossary, not a requirements document.

---

## Decision Flow

```
START: Plan / design / bug / feature with unclear domain or architecture
|
+- Run Grill Me discipline
|  +- Facts discoverable from repo/docs? -> Agent investigates
|  +- Real trade-off or preference? -> Ask user one question
|  +- Shared understanding incomplete? -> Continue grilling
|
+- Domain language emerges?
|  +- Existing glossary term matches? -> Use it consistently
|  +- Term is fuzzy or overloaded? -> Grill until precise
|  +- Term crystallized? -> Update CONTEXT.md
|
+- Durable architecture decision emerges?
|  +- Hard to reverse? -> Continue
|  +- Surprising without context? -> Continue
|  +- Real trade-off? -> Create or update ADR
|  +- Otherwise -> Do not create ADR
|
+- Summarize shared understanding and doc changes
   +- Wait for confirmation before implementation
```

---

## When To Use This Skill

- Feature discovery involving domain concepts or business language
- Bug investigations where terminology or expected behavior is unclear
- Architecture decisions that may need an ADR
- Codebase discussions where agent and user need a shared ubiquitous language
- Work spanning multiple bounded contexts

## When NOT To Use This Skill

- Trivial implementation tasks with already-clear language
- Pure mechanical edits where no domain terms or decisions change
- Emergency fixes where documentation should wait until stabilization
- One-off notes that do not belong in glossary or ADR form

---

## Relationship to DDD

This skill applies a lightweight Domain-Driven Design practice: use the grilling conversation to form and preserve a ubiquitous language. The user and agent should converge on the same terms before making design or implementation decisions.

The durable artifacts are intentionally small:
- `CONTEXT.md` holds glossary-level domain vocabulary
- `CONTEXT-MAP.md` points to multiple bounded contexts when needed
- ADRs record durable architecture decisions with rationale

---

## Outputs

- Confirmed shared understanding from the grilling session
- Facts discovered by agent exploration
- User decisions recorded explicitly
- Updated glossary entries when terms crystallize
- ADRs only for durable, surprising, trade-off-based decisions
- Clear next step: write PRD, decompose issues, implement, or continue discovery

---

## Common Pitfalls

| Pitfall | How to Recognize | How to Fix |
|---|---|---|
| **Duplicating Grill Me** | This page repeats the full grilling workflow | Link to Grill Me and document only the documentation extension |
| **Glossary as spec** | `CONTEXT.md` contains implementation details or unresolved plans | Keep `CONTEXT.md` to domain vocabulary only |
| **ADR overuse** | Every small choice becomes an ADR | Require hard-to-reverse, surprising, trade-off-based decisions |
| **Vocabulary drift** | Agent invents synonyms for existing concepts | Read and reuse domain docs before naming concepts |
| **Docs before understanding** | Agent creates files before terms or decisions are settled | Create docs lazily when knowledge crystallizes |

---

## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Grill With Docs composition | Matt Pocock `grill-with-docs` skill | Tool/Repository | Combines `grilling` with `domain-modeling` for engineering work | https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md |
| Domain modeling behavior | Matt Pocock `domain-modeling` skill | Tool/Repository | Active DDD glossary and ADR practice during design conversations | https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md |
| Behavioral refinements | User-provided video summary | Video/Summary | One-question discipline, confirmation gates, fact-vs-decision separation, and avoiding self-grilling | N/A |
