# AI-Assisted Engineering Handbook

A living GitHub-hosted handbook that captures practical approaches to AI-assisted software development.

This handbook helps you answer:

> **Given the engineering situation I'm currently facing, what AI-assisted approach should I follow, why should I follow it, and how can I execute it using my available tools?**

---

## Getting Started: Where Should I Go Next?

Start here to find the right guidance for your engineering situation.

See the **[Software Development Process](docs/processes/software-development.md)** for the complete end-to-end workflow.

```
I have work to do
│
├─ Do I understand the problem?
│
│  ├─ NO → Establish shared understanding
│  │        └─ [Discovery Phase] — Use AI to explore and align on requirements
│  │        └─ docs/processes/software-development.md#phase-1-discovery
│  │
│  └─ YES ↓
│
├─ Does this require significant design?
│
│  ├─ YES → Create PRD
│  │        └─ [Design Phase] — Formalize requirements and approach
│  │        └─ docs/processes/software-development.md#phase-2-design
│  │
│  └─ NO → Lightweight work item ↓
│
├─ Can the work be vertically sliced?
│
│  ├─ YES → Produce implementation issues
│  │        └─ [Decomposition Phase] — Break work into focused slices
│  │        └─ docs/processes/software-development.md#phase-3-decomposition
│  │
│  └─ NO → Investigation / de-risking
│          └─ [Decomposition Rainy Path] — Resolve dependencies first
│          └─ docs/processes/software-development.md#32-rainy-path-impossible-decomposition
│
├─ Is unattended execution appropriate?
│
│  ├─ YES → AFK workflow
│  │        └─ [AFK Implementation] — Autonomous AI-assisted development
│  │        └─ docs/processes/software-development.md#42-afk-implementation-ralph-loop
│  │
│  └─ NO → Supervised implementation
│           └─ [Supervised Implementation] — Step-by-step work with AI assistance
│           └─ docs/processes/software-development.md#43-supervised-implementation
│
└─ Implementation complete
   ↓
 [Validation Phase] → [Resolution Phase]
 └─ docs/processes/software-development.md#phase-5-validation
    docs/processes/software-development.md#phase-6-resolution
```

### Quick Navigation

- **[Processes](#processes)** — Structured workflows for engineering tasks
- **[Principles](#principles)** — Core ideas behind AI-assisted approaches
- **[Patterns](#patterns)** — Reusable solutions to common problems
- **[Playbooks](#playbooks)** — Step-by-step guides for specific scenarios
- **[Skills](#skills)** — Executable AI capabilities and workflows
- **[Adapters](#adapters)** — Tool-specific implementations (Claude, Copilot, OpenCode)
- **[Examples](#examples)** — Platform-specific demonstrations (Bash, PowerShell)
- **[References](#references)** — Research and provenance

---

## Handbook Structure

The handbook organizes knowledge across distinct layers to separate concepts from implementations.

### Navigation Layer

The top level that answers: **"What should I do now?"**

- **Processes** — Major workflows like "Design," "Implementation," "Review"
- **Scenarios** — Specific situations requiring particular approaches
- **Decisions** — Key decision points in engineering workflows

### Knowledge Layer

Foundational concepts and guidance.

- **Principles** — Core ideas and rationale (e.g., "Separate concept from implementation," "Leverage context effectively")
- **Patterns** — Reusable technical and organizational solutions
- **Playbooks** — Detailed procedures combining principles, patterns, and tactics

### Execution Layer

Operational tools and automation.

- **Skills** — Executable AI-driven capabilities (queries, analyses, implementations)
- **Agents** — AI workflows and autonomous execution modes

### Adapter Layer

Tool-specific and platform-specific implementations.

- **Harness Adapters** — Claude Code, GitHub Copilot, OpenCode
- **Platform Examples** — Bash (Unix/macOS), PowerShell (Windows)

### Maintenance Layer

Supporting human and AI-driven knowledge evolution.

- **Documentation Standards** — How to contribute and maintain the handbook
- **Authoring Instructions** — Guidelines for extending the system

---

## Core Design Principle

**Canonical knowledge is harness-agnostic. Harness-specific and platform-specific implementations are adapters or examples.**

For example, a technique demonstrated with Claude and Bash is not defined by those tools. The conceptual approach is separate from the mechanism. This allows reuse across:

- Multiple AI coding systems (Claude, Copilot, OpenCode, etc.)
- Multiple platforms (Windows PowerShell, Unix Bash, etc.)
- Multiple engineering scenarios (features, bugs, refactoring, migrations, etc.)

---

## Key Vocabulary

- **Process** — A structured workflow for a major engineering activity
- **Scenario** — A specific situation requiring particular guidance
- **Pattern** — A reusable solution to a recurring technical or organizational challenge
- **Principle** — A core idea underlying effective AI-assisted engineering
- **Playbook** — A step-by-step guide combining principles and patterns for a specific workflow
- **Skill** — An executable AI capability (can be manual or automated)
- **Adapter** — A harness-specific or platform-specific implementation
- **Example** — A concrete demonstration of a technique or approach
- **Reference** — Supporting documentation, research, or provenance

---

## How to Use This Handbook

### For Engineers

1. **Start with the navigation flow** at the top of this page to identify your situation
2. **Follow the linked processes** to find relevant guidance
3. **Explore principles and patterns** for rationale and reusability
4. **Check adapters and examples** for your specific tool or platform
5. **Link to your context** — bookmark processes relevant to your workflow

### For AI Agents

1. **Maintain consistent vocabulary** — use terms as defined in this handbook
2. **Avoid duplication** — reference existing concepts rather than redefining them
3. **Keep knowledge harness-agnostic** — separate concepts from tool implementations
4. **Update carefully** — track changes through version control and maintain backings in issues and PRDs
5. **Support human understanding** — ensure humans can navigate and verify your changes

---

## Repository Structure

```
ai-assisted-engineering-handbook/
├── README.md (this file)
├── PRD.md — Product requirements and design principles
├── CONTEXT.md — (future) Glossary and canonical domain definitions
├── docs/
│   ├── adr/ — (future) Architectural decision records
│   ├── processes/
│   │   └── software-development.md — Core end-to-end workflow
│   ├── principles/ — (future) Core concepts and rationale
│   ├── patterns/ — (future) Reusable solutions
│   ├── playbooks/ — (future) Step-by-step guides
│   ├── skills/ — (future) Executable AI capabilities
│   ├── adapters/ — (future) Harness-specific implementations
│   └── examples/ — (future) Platform-specific demonstrations
├── .agents/ — AI agent customization files
└── skills-lock.json — Installed AI skills registry
```

---

## What's Next?

This handbook is under active development. The foundation has been established, and work now focuses on formalizing key concepts:

- **#1: Bootstrap Handbook Navigation** ✓ Complete
  - Root navigation entry point with decision tree
- **#2: Document The Software-Development Process** ✓ Complete
  - End-to-end workflow from idea through delivery and feedback
- **#3: Add Core Patterns For The MVP Workflow** — (Next)
- **#7: Add Authoring And Maintenance Instructions** — (Planned)
- **#8: Record Provenance And Cross-Link The MVP** — (Planned)

---

## Contributing

See the handbook's development issues in GitHub Issues. The handbook is designed to evolve through:

1. Practical documentation of working approaches
2. Gradual organization and taxonomy refinement
3. Reuse and cross-linking to eliminate duplication
4. AI-assisted maintenance and evolution

To propose changes or additions, open an issue with:
- Description of the engineering situation you're addressing
- Which category (process, principle, pattern, playbook, skill, adapter, example)
- How it relates to existing handbook concepts
- Initial draft or reference material

---

## Key References

- **PRD.md** — Full product requirements, design principles, and initial process model
- **GitHub Issues** — Development roadmap and specification details

---

**Last Updated:** 2026-08-16  
**Status:** Handbook Bootstrap (Navigation and Core Process Documented)
