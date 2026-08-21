# Agent Context Artifacts

## Purpose

This reference explains when to use `AGENTS.md`, `CONTEXT.md`, skills, and task state in an agent-ready repository or workspace.

Use it when deciding where new agent-facing knowledge belongs, especially when applying [Context Management](../patterns/context-management.md), maintaining [Skills](../skills/README.md), or designing a [Unified Agent Workspace](../patterns/unified-agent-workspace.md).

---

## Core Mental Model

```text
AGENTS.md  -> routes and constrains
CONTEXT.md -> explains durable system understanding
Skill      -> teaches reusable task methodology
Plan/Issue -> tracks current work state
```

In shorter form:

> AGENTS routes. Context explains. Skills teach. Plans track.

An agent entering a repository usually needs four kinds of information:

| Artifact | Primary question | Scope | Typical lifetime |
|---|---|---|---|
| `AGENTS.md` | How should an agent work in this repository or directory? | Repository or directory | Long-lived |
| `CONTEXT.md` | What does the agent need to understand here? | Topic, subsystem, domain, feature, or project state | Evolves as understanding changes |
| Skill | How should the agent perform this kind of task? | Reusable across repositories or workspaces | Long-lived and versioned |
| Plan / issue / spec | What is happening in this specific piece of work? | Task, change, issue, or feature | Short-lived or archived |

---

## Where, What, How, Now

```text
AGENTS.md
  Where am I?
  What local rules apply?
  Where should I look next?

CONTEXT.md
  What is true here?
  Why does this exist?
  What decisions and relationships matter?

Skill
  How do I perform this class of task well?
  What procedure, checklist, or technique should I follow?

Plan / issue / spec
  What are we doing now?
  What is complete, blocked, or still pending?
```

This separation keeps agent knowledge progressively discoverable instead of turning one file into a giant permanent prompt.

---

## `AGENTS.md`: Orientation And Local Rules

`AGENTS.md` is instructional and navigational. It tells agents how to behave in a scope.

Put information in `AGENTS.md` when an agent entering that repository or directory needs it immediately to work safely.

Good contents include:

- Repository or directory purpose
- Important directories and ownership boundaries
- Build, test, lint, and verification commands
- Local conventions and constraints
- Files to read before specific kinds of work
- Pointers to relevant `CONTEXT.md` files
- Pointers to relevant skills
- Prohibitions such as generated files not to edit

Avoid putting full architecture histories, domain encyclopedias, generic coding standards, or long task progress notes in `AGENTS.md`. Link to the narrower artifact instead.

Example:

```text
Service: Customer Profile

Architecture:
  See docs/architecture/CONTEXT.md

Domain language:
  See docs/domain/CONTEXT.md

Current migration:
  See docs/migrations/customer-v2/CONTEXT.md

When writing Java:
  Use the java-standards skill

When reviewing code:
  Use the code-review skill
```

---

## `CONTEXT.md`: Durable Understanding

`CONTEXT.md` is descriptive. It captures what is true about a system, subsystem, domain, feature, or decision area so agents and humans do not rediscover it each time.

Put information in `CONTEXT.md` when it explains this particular system or area.

Good contents include:

- Domain concepts and ubiquitous language
- Architecture shape and component relationships
- Current system behavior
- Reasons behind important design decisions
- Integration constraints and invariants
- Local glossary entries
- Durable state that remains useful after the current task ends

A useful test:

> If all instructions telling an agent what to do were removed, would this still help a human understand the system?

If yes, it probably belongs in `CONTEXT.md`.

Example:

```text
The drop-off service monitors applications that have stopped progressing.

An application becomes eligible for dropping after two hours of inactivity.

Dropped applications cause a CRM lead to be generated.

The service uses an outbox because Kafka publication and database updates
cannot participate in one transaction.

The scheduler uses SKIP LOCKED because multiple pods may execute concurrently.
```

---

## Skill: Reusable Capability

A skill is procedural capability. It codifies reusable expertise for performing a class of task.

Put information in a skill when it teaches how to perform work that should apply across repositories or workspaces.

Good skill topics include:

- Code review
- Bug investigation
- Threat modeling
- Writing PRDs
- Decomposing work into issues
- Applying a language or framework standard
- Running an incident analysis
- Reviewing architecture

A skill should not explain a specific service in detail. It should define the method, decision points, checks, outputs, and verification criteria. Repository-specific facts should come from `AGENTS.md`, `CONTEXT.md`, source code, issues, and specs.

Example:

```text
Code review skill:
1. Understand the change intent.
2. Inspect the diff before surrounding implementation.
3. Check correctness and failure modes.
4. Check maintainability.
5. Distinguish blocking issues from suggestions.
6. Phrase comments conversationally.
```

---

## Task State: Current Work

Some information belongs in neither `AGENTS.md`, `CONTEXT.md`, nor a skill.

Use a plan, issue, spec, ADR, or work log for information about the current piece of work.

Good contents include:

- Feature scope and acceptance criteria
- Current implementation progress
- Open questions for this task
- Failing tests for this task
- Temporary blockers
- Decisions pending human review

Do not put ephemeral task status into `AGENTS.md`; every future agent would pay the cost of reading stale work state. Do not put task-specific progress into a generic skill; it would make the skill less reusable.

---

## Decision Tree

```text
Is this reusable knowledge about HOW to perform a task?
  -> Skill

Is this knowledge about WHAT this system is, why it exists,
or how it currently works?
  -> CONTEXT.md

Does an agent entering this scope need this instruction or pointer
immediately to behave correctly?
  -> AGENTS.md

Is this primarily about work currently being performed?
  -> Plan / spec / issue / work log
```

---

## Context Vs Instruction

The deepest distinction is context versus instruction.

| Statement | Better home | Why |
|---|---|---|
| The settlement process executes every evening at 22:00. | `CONTEXT.md` | Describes what is true |
| Never modify settlement scheduling without running the settlement regression suite. | `AGENTS.md` | Constrains local agent behavior |
| How to determine the right integration-test scope. | Skill | Teaches reusable method |
| Settlement scheduler migration step 3 is blocked by a failing test. | Plan / issue | Tracks current work state |

`CONTEXT.md` describes the world. `AGENTS.md` constrains agent behavior. Skills teach agent behavior. Plans track task state.

---

## Handling Deliberate Overlap

Some ideas belong in more than one artifact, but for different reasons.

Example: an organization uses hexagonal architecture.

| Artifact | Content |
|---|---|
| Skill | How to design and review software using hexagonal architecture |
| `docs/architecture/CONTEXT.md` | This service's ports, adapters, and reasons for using the architecture |
| `AGENTS.md` | This repository follows hexagonal architecture; see the architecture context; use the skill when adding adapters |

This is not duplication if each artifact plays a different role.

---

## Progressive Disclosure

Avoid this failure mode:

```text
AGENTS.md
  architecture
  coding standards
  git commands
  system history
  deployment instructions
  feature descriptions
  code review process
  testing philosophy
  security guidance
```

Prefer this structure:

```text
repository/
  AGENTS.md
  docs/
    architecture/CONTEXT.md
    domain/CONTEXT.md
    features/customer-v2/CONTEXT.md
  .agents/
    skills/
      java-standards/
      code-review/
  issues/
  specs/
  plans/
```

`AGENTS.md` becomes the router. Context files provide scoped understanding. Skills provide reusable method. Plans, specs, and issues carry task state.

---

## Relationship To ICM And Agent Loops

ICM-style context engineering treats the filesystem as an external memory and navigation system. The goal is not to load everything into the prompt; it is to let the agent reconstruct the smallest useful mental model for the task.

Karpathy-style agent loops operate over that memory:

```text
Agent
  -> AGENTS.md: local routing and rules
  -> CONTEXT.md: durable understanding
  -> Skill: task method
  -> Plan / issue: current work state
  -> implement / verify / adjust loop
```

The artifacts shape what the agent reads before and during the loop.

---

## Maintenance Rules

- Update a skill when the reusable method changes.
- Update `CONTEXT.md` when the system understanding, domain language, architecture, or durable decisions change.
- Update `AGENTS.md` when local operating rules, navigation, or required verification commands change.
- Update the plan, spec, issue, or work log when the current task state changes.
- Prefer links over copying content across artifacts.
- Put knowledge beside the smallest scope for which it is true.

---

## Related Concepts

- [Context Management](../patterns/context-management.md) - Pattern for deliberately selecting relevant context
- [Repository Mental Model Tools](repository-mental-model-tools.md) - Tools and substrates for repository-scale mental models
- [Unified Agent Workspace](../patterns/unified-agent-workspace.md) - Multi-repository workspace pattern for agent-visible engineering systems
- [Skills](../skills/README.md) - Executable AI capabilities and their lifecycle
- [Grill With Docs](../skills/grill-with-docs.md) - Skill that updates `CONTEXT.md` when domain language crystallizes

---

## Sources And Provenance

| Source | Type | Context | URL |
|---|---|---|---|
| User-provided synthesis of AGENTS, CONTEXT, skills, ICM, and Karpathy-style loops | Practitioner synthesis | Contributed the routing/explanation/teaching/tracking mental model and decision tree | N/A |
| Handbook maintenance rules in `AGENTS.md` | Repository convention | Reinforces layer separation, cross-linking, and avoiding duplicate concepts | ../../AGENTS.md |

---

**Last Updated:** 2026-08-20  
**Status:** Reference Mental Model
