# Product Requirements Document

## 1. Working Title

**Living AI-Assisted Engineering Handbook**

The name is intentionally provisional. The repository is expected to evolve beyond a conventional book into a version-controlled knowledge and process system for AI-assisted software engineering.

---

## 2. Product Vision

Create an online, GitHub-hosted, living handbook that captures, organizes, evolves, and operationalizes practical approaches to AI-assisted software development.

The handbook should help answer:

> **Given the engineering situation I am currently facing, what AI-assisted approach should I follow, why should I follow it, and how can I execute it using my available tools?**

The repository should combine:

* human-readable engineering guidance;
* navigable process flows;
* reusable principles, patterns, and playbooks;
* executable AI skills and agent instructions;
* harness-specific adapters where necessary;
* operating-system-specific examples where necessary;
* provenance and references;
* instructions that allow AI agents to maintain and evolve the handbook itself.

The repository should remain usable by both **humans and AI agents**.

---

## 3. Problem Statement

AI-assisted software engineering practices are evolving rapidly.

Useful techniques are currently scattered across:

* blog posts;
* videos;
* conference talks;
* social media;
* GitHub repositories;
* vendor documentation;
* personal notes;
* AI conversations;
* examples from practitioners;
* tool-specific configuration.

These ideas are often presented in ways that are specific to:

* Claude Code;
* GitHub Copilot;
* OpenCode;
* a particular LLM;
* Bash;
* PowerShell;
* macOS/Linux;
* Windows;
* a specific repository structure.

This creates several problems.

### 3.1 Knowledge becomes fragmented

Concepts such as:

* Ralph Loops;
* context management;
* sub-agents;
* TDD;
* tracer bullets;
* vertical slicing;
* PRD generation;
* agent review;
* codebase exploration;
* autonomous implementation;

may appear in many different sources without a canonical place in which they are related to each other.

### 3.2 Tool implementations become confused with concepts

An implementation demonstrated using Claude Code and Bash may accidentally be treated as though Claude and Bash are intrinsic to the underlying technique.

They are not.

The conceptual process should be separated from the mechanism used to execute it.

### 3.3 Knowledge is difficult to reuse

A useful technique discovered for feature development may also apply to:

* bug fixing;
* refactoring;
* migrations;
* repository exploration;
* documentation;
* overnight/AFK development.

Duplicating the explanation in each process leads to documentation drift.

### 3.4 Documentation becomes stale

Maintaining a large body of interconnected process documentation manually eventually becomes burdensome.

AI agents should therefore be capable of understanding the repository structure and incorporating new knowledge into it without creating unnecessary duplication.

---

# 4. Goals

## 4.1 Primary Goals

The system shall:

1. Provide a navigable map of AI-assisted engineering processes.
2. Help a developer determine what approach to use for a particular situation.
3. Separate reusable concepts from specific tools and implementations.
4. Remain usable across multiple AI coding harnesses.
5. Support both Windows and macOS development environments.
6. Allow concepts to reference one another without duplicating documentation.
7. Allow AI agents to maintain and extend the knowledge system.
8. Record where important ideas originated.
9. Evolve incrementally rather than requiring a perfect taxonomy upfront.
10. Eventually allow documented processes to become executable agent workflows.

---

# 5. Non-Goals

The initial version will **not** attempt to:

* define the perfect final repository taxonomy;
* support every AI coding harness;
* automate every documented process;
* build a custom documentation web application;
* replace vendor documentation;
* create an AI framework of its own;
* require a particular LLM;
* require a monorepo;
* prescribe a single software-development methodology;
* eliminate human judgement from software engineering.

GitHub Markdown should initially provide the primary documentation interface.

---

# 6. Target User

Initially, the primary user is a software engineer experimenting with and refining AI-assisted engineering processes.

The developer:

* works with multiple AI coding systems;
* currently needs portability across **Claude Code, GitHub Copilot, and OpenCode**;
* works on both **Windows and a MacBook**;
* may therefore require PowerShell, Bash, or other platform-specific implementations;
* wants to experiment with multi-agent development;
* wants to reduce unnecessary LLM token usage;
* wants reusable skills, agents, patterns, and workflows rather than vendor lock-in;
* wants accumulated learning to become a long-term engineering knowledge asset.

The design should nevertheless remain generic enough that other developers could eventually use the repository.

---

# 7. Core Design Principle

The repository shall follow the rule:

> **Canonical knowledge is harness-agnostic. Harness-specific and platform-specific implementations are adapters or examples.**

For example:

```text
Ralph Loop
│
├── Core concept
│
├── Process usage
│
├── Implementations
│   ├── Bash
│   └── PowerShell
│
└── Harness adapters
    ├── Claude
    ├── GitHub Copilot
    └── OpenCode
```

A Claude/Bash example must not become the canonical definition of a Ralph Loop.

---

# 8. Conceptual Architecture

The knowledge system should gradually develop several distinct layers.

```text
                     README
                        │
                Navigation Layer
                        │
             "What should I do now?"
                        │
       ┌────────────────┼────────────────┐
       │                │                │
   Processes        Scenarios        Decisions
       │
       ▼
                Knowledge Layer
       ┌────────────┼─────────────┐
       │            │             │
 Principles      Patterns      Playbooks
                                  │
                                  ▼
                          Execution Layer
                         Skills / Agents
                                  │
                                  ▼
                           Adapter Layer
                    ┌─────────────┼─────────────┐
                    │             │             │
                  Claude       OpenCode      Copilot
                    │             │             │
                    └─────────────┼─────────────┘
                                  │
                         Platform Examples
                           Bash / PowerShell

                                  │
                                  ▼
                         Maintenance Layer
                  AI-assisted knowledge evolution
```

---

# 9. Navigation Model

The repository should behave more like a **textual mind map** than a conventional book.

The root README should primarily answer:

> **Where should I go next?**

For example:

```text
I have work to do
│
├─ Do I understand the problem?
│
│  ├─ NO → Establish shared understanding
│  │        └─ Grill Me
│  │
│  └─ YES
│
├─ Does this require significant design?
│
│  ├─ YES → Create PRD
│  └─ NO  → Lightweight work item
│
├─ Can the work be vertically sliced?
│
│  ├─ YES → Produce implementation issues
│  └─ NO  → Investigation / de-risking
│
├─ Is unattended execution appropriate?
│
│  ├─ YES → AFK workflow / Ralph Loop
│  └─ NO  → Supervised implementation
│
└─ Implementation complete
       ↓
     Review
       ↓
       QA
       ↓
     Feedback
```

Individual nodes link to deeper documentation.

---

# 10. Initial Reference Process

The initial process model will be based on industry best practices for AI-assisted software development.

At a high level:

```text
Idea / Brief
     ↓
Grill Me
     ↓
Shared Design Concept
     ↓
PRD
     ↓
Vertically Sliced Issues
     ↓
Human Review
     ↓
AFK Agent Execution
     ↓
TDD Implementation
     ↓
Feedback Loops
     ↓
Fresh-Context Review
     ↓
Human QA
     ↓
New Issues if Required
     ↓
Team Review
```

The original workflow should be treated as a **starting process**, not as an immutable methodology.

The handbook exists specifically so that this workflow can be challenged and evolved.

---

# 11. Knowledge Types

The initial taxonomy should remain deliberately small.

## 11.1 Processes

End-to-end workflows that answer:

> What sequence of activities should I follow?

Examples:

* feature development;
* bug fixing;
* refactoring;
* unfamiliar codebase;
* AFK implementation.

---

## 11.2 Patterns

Reusable techniques that can appear in many processes.

Examples:

* Ralph Loops;
* vertical slicing;
* tracer bullets;
* context management;
* TDD;
* feedback loops;
* sub-agent delegation.

---

## 11.3 Playbooks

Practical procedures that answer:

> How should I perform this activity?

Examples:

* explore a repository;
* review implementation;
* investigate a bug;
* create a PRD;
* decompose work.

---

## 11.4 Principles

Underlying reasoning that explains why practices exist.

Examples:

* human-in-the-loop;
* context economy;
* fresh-context review;
* fail fast;
* separation of orchestration and exploration.

---

## 11.5 Skills

Executable or semi-executable AI instructions implementing a specific capability.

Examples:

* Grill Me;
* Write PRD;
* PRD to Issues;
* Repository Explorer;
* Code Reviewer.

---

## 11.6 Scenarios

Entry points based on the developer's situation.

Examples:

```text
"I have a vague feature idea."

"I need to fix a production bug."

"I have inherited an unfamiliar repository."

"I want an agent to work unattended."

"I need to refactor something safely."
```

These should primarily link into existing processes, patterns and playbooks rather than duplicate them.

---

## 11.7 References

Sources from which ideas originated.

Possible sources include:

* articles;
* GitHub repositories;
* documentation;
* videos;
* transcripts;
* conference talks;
* practitioners;
* experiments.

References provide provenance but should not control the repository taxonomy.

---

# 12. Harness Portability

The first supported AI engineering harnesses are:

* Claude Code;
* GitHub Copilot;
* OpenCode.

Canonical documentation must not assume any one of these.

Where an implementation differs, the documentation should explicitly separate those differences.

For example:

```text
patterns/
  ralph-loop/
    README.md

    examples/
      bash.md
      powershell.md

    adapters/
      claude.md
      copilot.md
      opencode.md
```

Possible future harnesses should be addable without restructuring the canonical documentation.

---

# 13. Platform Portability

The repository must explicitly support development from:

* Windows;
* macOS.

Platform-neutral approaches should be preferred whenever practical.

Where platform differences matter, they should be documented as implementations rather than separate concepts.

For example:

```text
Ralph Loop

Canonical algorithm
      │
      ├── Bash implementation
      │      macOS / Linux
      │
      └── PowerShell implementation
             Windows
             optionally macOS/Linux via pwsh
```

Where useful, Python or another cross-platform implementation may also be provided.

---

# 14. AI-Assisted Knowledge Maintenance

A fundamental capability of the repository is that an AI agent should be able to maintain the handbook itself.

The user should be able to provide:

* an idea;
* a URL;
* a transcript;
* documentation;
* an existing implementation;
* an AI conversation;
* experimental findings;

and instruct the agent to:

> **Integrate the useful knowledge into the handbook.**

The agent should not interpret this as:

> Create a new Markdown file.

Instead, integration is a structured process.

---

# 15. Knowledge Integration Workflow

The target authoring workflow is:

```text
SOURCE
   │
   ▼
INGEST
   │
   ▼
UNDERSTAND
   │
   ▼
CLASSIFY
   │
   ▼
SEARCH EXISTING KNOWLEDGE
   │
   ▼
COMPARE
   │
   ├─ Already documented
   ├─ Extends existing knowledge
   └─ Introduces new concept
   │
   ▼
DECOMPOSE
   │
   ├─ Canonical concept
   ├─ Process implication
   ├─ Playbook
   ├─ Harness-specific detail
   ├─ Platform-specific implementation
   └─ Reference/provenance
   │
   ▼
INTEGRATE
   │
   ▼
CROSS-LINK
   │
   ▼
VALIDATE
   │
   ▼
REPORT CHANGES
```

---

# 16. Example Knowledge-Maintenance Scenario

The user provides:

> I would like a code implementer agent documented with a Ralph Loop pattern in Claude using a Bash script, with an equivalent PowerShell implementation.

The maintenance agent should inspect the repository before deciding what to create.

A possible result could be:

```text
patterns/ralph-loop/README.md
    UPDATE if the source reveals additional conceptual information

patterns/ralph-loop/examples/bash.md
    ADD or UPDATE Bash implementation

patterns/ralph-loop/examples/powershell.md
    ADD PowerShell equivalent

patterns/ralph-loop/adapters/claude.md
    ADD Claude-specific behaviour where relevant

processes/feature-development.md
    LINK Ralph Loop from AFK execution

references/matt-pocock.md
    RECORD provenance
```

The agent must avoid creating:

```text
matt-pocock-claude-bash-ralph-loop.md
```

unless there is a compelling reason for such a document to exist.

---

# 17. Authoring Principle: Enrich Before Creating

The maintenance agent shall follow:

> **Prefer linking and enrichment over duplication.**

Before creating a new knowledge document, the agent should determine whether the supplied information represents:

```text
[ ] A new principle
[ ] A new pattern
[ ] A new process
[ ] A new playbook
[ ] A new skill
[ ] A new harness adapter
[ ] A new platform implementation
[ ] A new scenario
[ ] Supporting evidence for existing knowledge
[ ] Merely a reference
```

Multiple classifications may apply.

---

# 18. Repository Self-Description

The repository should contain instructions describing how the repository itself works.

A root `AGENTS.md` should eventually act as the entry point for an AI operating within the repository.

It should remain relatively concise.

Conceptually:

```text
This repository is a living knowledge system for
AI-assisted software engineering.

When incorporating new knowledge:

1. Understand the source.
2. Read the repository authoring rules.
3. Search existing knowledge.
4. Classify the new information.
5. Prefer modifying canonical knowledge over duplication.
6. Separate concepts from harness implementations.
7. Separate concepts from operating-system implementations.
8. Cross-link affected processes and scenarios.
9. Preserve provenance.
10. Validate repository navigation.
11. Summarize the changes made.
```

More detailed instructions should live elsewhere and be linked from this file.

---

# 19. Initial Repository Structure

A deliberately minimal starting structure is preferred.

```text
/
├── README.md
├── AGENTS.md
│
├── processes/
│   └── software-development.md
│
├── patterns/
│   ├── ralph-loops.md
│   ├── vertical-slicing.md
│   ├── tdd.md
│   └── context-management.md
│
├── playbooks/
│   ├── repository-exploration.md
│   ├── write-prd.md
│   └── review-code.md
│
├── principles/
│   ├── context-economy.md
│   └── human-in-the-loop.md
│
├── skills/
│   ├── grill-me.md
│   ├── write-prd.md
│   └── prd-to-issues.md
│
├── scenarios/
│   ├── new-feature.md
│   ├── bug-fix.md
│   └── unfamiliar-codebase.md
│
├── adapters/
│   ├── claude/
│   ├── copilot/
│   └── opencode/
│
├── references/
│
└── system/
    └── authoring/
        ├── README.md
        ├── classify.md
        ├── integrate.md
        └── validate.md
```

This is an initial hypothesis rather than a fixed schema.

Directories should only be introduced when they provide demonstrated value.

---

# 20. Progressive Executability

The handbook should support a progression from human knowledge to automation.

```text
Principle
    ↓
Pattern
    ↓
Playbook
    ↓
Skill
    ↓
Agent
    ↓
Automated Workflow
```

Example:

```text
Context Economy
      ↓
Repository exploration before implementation
      ↓
Repository Exploration Playbook
      ↓
repository-explorer skill
      ↓
Repository Explorer sub-agent
```

Documentation should remain valuable even when no automation exists.

Automation should therefore implement the documented process rather than replace its explanation.

---

# 21. Agent Portability

Where agents or skills are created, their behavioural definitions should be as harness-neutral as possible.

The desired conceptual arrangement is:

```text
                  Canonical Agent Definition
                           │
             common/code-reviewer.md
                           │
          ┌────────────────┼────────────────┐
          │                │                │
       Claude           Copilot          OpenCode
       wrapper          wrapper           wrapper
```

Wrappers should contain only the harness-specific metadata or instructions required for that harness.

Harness capabilities should not leak unnecessarily into canonical behavioural definitions.

---

# 22. Rainy Paths

Processes should explicitly document failure and recovery paths.

Examples include:

* requirements remain unclear;
* PRD decomposition produces horizontal technical layers;
* an agent becomes stuck;
* tests fail;
* type checking fails;
* review discovers architectural problems;
* human QA rejects the behaviour;
* harness capabilities differ;
* source information conflicts with existing knowledge;
* documentation introduces broken links;
* an implementation works in Bash but not PowerShell;
* a technique cannot be made harness-neutral.

Rainy paths are first-class parts of the process rather than footnotes.

---

# 23. Human Responsibilities

AI agents may:

* explore;
* research;
* classify;
* summarize;
* propose;
* implement;
* review;
* cross-link;
* validate documentation.

Humans remain responsible for:

* judgement;
* taste;
* prioritization;
* architectural intent;
* risk acceptance;
* evaluating whether processes actually work;
* deciding whether new techniques should become canonical.

The handbook should make human intervention points explicit.

---

# 24. Version Control as Knowledge History

Git should provide the history of how the methodology evolves.

Important process changes should therefore be reviewable as diffs.

This allows the repository to answer not only:

> What is our current approach?

but potentially:

> Why did our approach change?

Branches and pull requests may be used to experiment with alternative approaches before they become canonical.

---

# 25. Initial User Stories

### US-01 — Navigate from a problem

As a developer,
I want to start from the situation I am facing,
so that I can determine which process or technique to follow.

### US-02 — Understand a concept independently of tooling

As a developer,
I want the Ralph Loop explained independently of Claude, Bash, or another implementation,
so that I understand the underlying technique.

### US-03 — Find harness-specific guidance

As a Claude, Copilot, or OpenCode user,
I want to see how a canonical process maps to my harness,
so that I can apply it without vendor-specific knowledge contaminating the main documentation.

### US-04 — Find platform-specific examples

As a developer using both Windows and macOS,
I want equivalent PowerShell and Bash examples where appropriate,
so that I can use the same engineering approach in either environment.

### US-05 — Introduce new knowledge

As a developer,
I want to give an AI agent an article, transcript, idea, or implementation,
so that the agent can integrate useful information into the existing handbook.

### US-06 — Avoid duplication

As a handbook maintainer,
I want the AI agent to search existing documentation before creating new content,
so that the knowledge base remains coherent.

### US-07 — Preserve provenance

As a reader,
I want to know where significant techniques originated,
so that I can inspect the source material myself.

### US-08 — Evolve documentation into automation

As a developer,
I want useful playbooks to be implementable as skills or agents,
so that well-understood processes can gradually become executable.

### US-09 — Review knowledge changes

As a human maintainer,
I want AI-generated handbook changes to be visible as normal Git diffs,
so that I retain control over what becomes canonical.

---

# 26. MVP

The first useful version should remain small.

## MVP Deliverables

### Root navigation

`README.md`

Contains a human-readable pseudocode representation of the initial software-development workflow.

### Initial process

`processes/software-development.md`

Captures the end-to-end workflow including rainy paths and decision points.

### Initial patterns

At minimum:

```text
patterns/
  ralph-loops.md
  vertical-slicing.md
  context-management.md
  tdd.md
```

### Initial skills documentation

```text
skills/
  grill-me.md
  write-prd.md
  prd-to-issues.md
```

### Initial authoring instructions

```text
AGENTS.md

system/authoring/
  README.md
  classify.md
  integrate.md
```

### Portability demonstration

Ralph Loop should act as the first proof that the model works.

It should eventually contain:

* harness-neutral explanation;
* Bash implementation;
* PowerShell implementation;
* Claude-specific notes where needed;
* OpenCode notes where needed;
* Copilot notes where needed.

This will test both **harness portability** and **platform portability**.

---

# 27. MVP Success Criteria

The MVP should be considered successful when the following workflow is possible:

1. A developer opens `README.md`.
2. They can navigate from a software-development situation to the relevant guidance.
3. Shared concepts are linked rather than duplicated.
4. At least one concept is documented independently of its implementation.
5. That concept has both Bash and PowerShell examples.
6. Harness-specific differences are isolated from canonical documentation.
7. An AI agent can read `AGENTS.md` and understand how new knowledge should be integrated.
8. Given a new source, the agent can correctly decide whether to create, modify, link, or ignore documentation.
9. All changes remain understandable through ordinary Git diffs.
10. The resulting repository remains understandable without requiring an AI agent.

---

# 28. Future Capabilities

Potential future evolution includes:

* automated Markdown link validation;
* documentation linting;
* metadata/front matter;
* automated knowledge graph generation;
* Mermaid process diagrams;
* automatic indexes;
* source freshness checks;
* experiment/result records;
* reusable agent personas;
* harness wrapper generation;
* skill generation from mature playbooks;
* process maturity ratings;
* decision records explaining methodological changes;
* automated detection of duplicated concepts;
* test cases for agent behaviours;
* evaluation suites for comparing process variants;
* synchronization with external skill/plugin repositories;
* publishing via GitHub Pages or a documentation generator.

These should not be prerequisites for the MVP.

---

# 29. Open Design Questions

The following should be allowed to emerge through experimentation:

1. How much metadata should each document contain?
2. Should Markdown front matter eventually be standardized?
3. Should harness adapters live beside concepts or in a centralized adapter tree?
4. When should a playbook become a skill?
5. When should a skill become an agent?
6. How should experimental knowledge be distinguished from established guidance?
7. Should sources have confidence or evidence ratings?
8. How should conflicting approaches be represented?
9. How should obsolete practices be deprecated rather than simply deleted?
10. Should agent-maintenance workflows automatically create branches or pull requests?
11. How should knowledge across external repositories be referenced?
12. How should generated artifacts be distinguished from human-authored canonical material?
13. Should executable examples have automated tests?
14. How should compatibility across Claude, Copilot, and OpenCode be continuously validated?

These are intentionally **not** resolved in the first iteration.

---

# 30. Guiding Heuristics

Until a more formal design emerges, the repository should follow these heuristics:

> **README tells me where to go.**

> **Processes tell me what to do.**

> **Playbooks tell me how to do it.**

> **Patterns describe reusable techniques.**

> **Principles explain why.**

> **Skills and agents automate mature processes.**

> **Adapters explain tool-specific differences.**

> **Examples explain platform-specific execution.**

> **References explain where ideas came from.**

> **The authoring system teaches AI how to evolve everything above.**

---

# 31. North-Star Experience

Eventually, the desired interaction should be as simple as:

> Here is a transcript from an interesting talk about AI coding. Analyze it and integrate anything genuinely useful into the handbook.

The AI should then be capable of:

```text
Understand source
      ↓
Explore handbook
      ↓
Identify novel information
      ↓
Find related concepts
      ↓
Separate principle from implementation
      ↓
Separate harness-specific detail
      ↓
Separate platform-specific detail
      ↓
Create or update appropriate documents
      ↓
Cross-link affected processes
      ↓
Record provenance
      ↓
Validate structure
      ↓
Present changes for human review
```

The resulting knowledge should become part of a continuously improving, portable, executable methodology for AI-assisted software engineering.
