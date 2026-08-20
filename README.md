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
  - [Software Development Process](docs/processes/software-development.md) — Core end-to-end workflow
- **[Patterns](#patterns)** — Reusable solutions to common problems
  - [Goal Loop](docs/patterns/goal-loop/README.md) — Evidence-driven completion loop with independent evaluation
  - [TDD Goal Loop](docs/patterns/tdd-goal-loop/README.md) — Multi-agent TDD loop for vertical slices
  - [Ralph Loop](docs/patterns/ralph-loop/README.md) — Autonomous agent implementation feedback loop
  - [Vertical Slicing](docs/patterns/vertical-slicing.md) — Break work into independent slices
  - [Tracer Bullets](docs/patterns/tracer-bullets.md) — Minimal end-to-end implementation
  - [Unified Agent Workspace](docs/patterns/unified-agent-workspace.md) — Virtual monorepo for multi-repo agentic engineering
  - [Scheduled Agentic Work](docs/patterns/scheduled-agentic-work.md) — Recurring repository maintenance with interchangeable agent executors
  - [Context Management](docs/patterns/context-management.md) — Provide focused information
  - [Test-Driven Development](docs/patterns/test-driven-development.md) — Red → Green → Refactor
  - [Feedback Loops](docs/patterns/feedback-loops.md) — Learn and improve continuously
- **[Principles](#principles)** — Core concepts and rationale
- **[Playbooks](#playbooks)** — Step-by-step guides for specific scenarios
  - [Repository Exploration](docs/playbooks/repository-exploration.md) — Understanding existing codebases
  - [Create a PRD](docs/playbooks/create-prd.md) — Writing clear requirements
  - [Decompose Work](docs/playbooks/decompose-work.md) — Breaking PRDs into implementation issues
  - [Review Implementation](docs/playbooks/review-implementation.md) — Systematic code review
  - [Investigate a Bug](docs/playbooks/investigate-bug.md) — Finding root causes and fixing bugs
  - [Configure Claude Code Hooks](docs/playbooks/configure-claude-code-hooks.md) — Add Claude Code guardrails, routing, and quality checks
  - [Sync Skills With Upstream Repositories](docs/playbooks/sync-skills-with-upstream.md) — Keep skills current with upstream repos through scheduled PRs
  - [Agent Workspace Lab](docs/playbooks/agent-workspace-lab.md) — Prove portable skills, agents, bootstrap, CI, and scheduled custodians
- **[Skills](#skills)** — Executable AI capabilities and workflows
  - [Grill Me](docs/skills/grill-me.md) — Establish shared understanding through discovery
  - [Grill With Docs](docs/skills/grill-with-docs.md) — Combine grilling with domain docs and ADRs
  - [Write PRD / To Spec](docs/skills/write-prd.md) — Create formal specifications
  - [PRD to Issues](docs/skills/prd-to-issues.md) — Decompose specifications into implementation issues
  - [Skills Overview](docs/skills/README.md) — Complete guide to all skills and maturity criteria
- **[Adapters](#adapters)** — Tool-specific implementations (Claude, Copilot, OpenCode)
  - [Claude Code Hooks](docs/adapters/claude-code-hooks/README.md) — Hook recipes for Claude Code guardrails and workflow automation
- **[Examples](#examples)** — Platform-specific demonstrations (Bash, PowerShell)
- **[References & Provenance](#references--provenance)** — Source attribution and concept relationships
  - [References Overview](docs/references/README.md) — Provenance and cross-linking strategy
  - [Repository Mental Model Tools](docs/references/repository-mental-model-tools.md) — Codebase graph, documentation, and code-intelligence tool ecosystem
  - [Provenance Guide](docs/references/provenance.md) — How to record sources and attribution
  - [Cross-Link Index](docs/references/cross-links.md) — Concept relationships and navigation

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

👉 **See [AGENTS.md](AGENTS.md) for complete maintenance and operational guidance.**

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
│   ├── patterns/
│   │   ├── goal-loop/
│   │   │   ├── README.md — Canonical Goal Loop pattern definition
│   │   │   └── adapters/
│   │   │       ├── claude.md — Claude Code /goal mapping
│   │   │       └── opencode.md — OpenCode command and evaluator mapping
│   │   ├── ralph-loop/
│   │   │   ├── README.md — Canonical Ralph Loop pattern definition
│   │   │   ├── examples/
│   │   │   │   ├── bash.md — Bash/Unix implementation
│   │   │   │   └── powershell.md — PowerShell/Windows implementation
│   │   │   └── adapters/
│   │   │       ├── claude.md — Claude API integration
│   │   │       ├── copilot.md — GitHub Copilot integration
│   │   │       └── opencode.md — OpenCode integration
│   │   ├── vertical-slicing.md — Break work into independent slices
│   │   ├── tracer-bullets.md — Minimal end-to-end implementation
│   │   ├── unified-agent-workspace.md — Virtual monorepo for multi-repo agentic engineering
│   │   ├── scheduled-agentic-work.md — Recurring repository maintenance with interchangeable agent executors
│   │   ├── context-management.md — Provide focused information
│   │   ├── test-driven-development.md — Red → Green → Refactor
│   │   └── feedback-loops.md — Learn and improve continuously
│   ├── principles/ — (future) Core concepts and rationale
│   ├── playbooks/
│   │   ├── repository-exploration.md — Understand existing codebases
│   │   ├── create-prd.md — Write clear, actionable requirements
│   │   ├── decompose-work.md — Convert PRDs into implementation issues
│   │   ├── review-implementation.md — Review code systematically
│   │   ├── investigate-bug.md — Find and fix bugs methodically
│   │   ├── configure-claude-code-hooks.md — Configure Claude Code guardrails and workflow hooks
│   │   ├── sync-skills-with-upstream.md — Keep skills current with upstream repository changes
│   │   └── agent-workspace-lab.md — Prove portable skills, agents, bootstrap, CI, and scheduled custodians
│   ├── skills/
│   │   ├── grill-me.md — Executable discovery specification
│   │   ├── write-prd.md — Executable PRD writing specification
│   │   ├── prd-to-issues.md — Executable decomposition specification
│   │   ├── README.md — Skills overview and navigation
│   │   └── skill-maturity-criteria.md — When playbooks become skills
│   ├── adapters/
│   │   └── claude-code-hooks/ — Claude Code hook examples and lifecycle recipes
│   └── examples/ — (future) Platform-specific demonstrations
├── .agents/ — Repository-owned AI skills and optional ignored external runtime material
└── skills-lock.json — Committed registry for reproducible external skill installs
```

---

## What's Next?

This handbook is under active development. The foundation has been established, with core patterns documented:

- **#1: Bootstrap Handbook Navigation** ✓ Complete
  - Root navigation entry point with decision tree
- **#2: Document The Software-Development Process** ✓ Complete
  - End-to-end workflow from idea through delivery and feedback
- **#3: Add Core Patterns For The MVP Workflow** ✓ Complete
  - Vertical Slicing, Tracer Bullets, Context Management, TDD, Feedback Loops
- **#4: Add Playbooks For Human And Agent Work** ✓ Complete
  - Repository Exploration, Create PRD, Decompose Work, Review Implementation, Investigate Bug
- **#5: Add Initial Skills Documentation** ✓ Complete
  - Grill Me, Write PRD, PRD to Issues (with skill maturity criteria)
- **#6: Prove Portability With Ralph Loops** ✓ Complete
  - Core Ralph Loop pattern with Bash/PowerShell examples and Claude/Copilot/OpenCode adapters
- **#7: Add Authoring And Maintenance Instructions** ✓ Complete
  - AI agent instructions (AGENTS.md), classification, integration, and validation guides
- **#8: Record Provenance And Cross-Link The MVP** ✓ Complete
  - Reference system with provenance and cross-linking guidelines
  - Ralph Loop updated with sources and concept relationships
  - Complete cross-link index for handbook coherence
- **#9: Next Phase** — (Planning phase)

---

## Contributing to the Handbook

The handbook evolves through practical documentation and AI-assisted maintenance.

### For Humans: How to Contribute

1. **Identify what you want to contribute**
   - An idea or technique you've found works
   - A working implementation (code, script, etc.)
   - A blog post, article, or external resource
   - A lesson learned from your experience
   - A new tool or approach

2. **Ask an AI agent to integrate it**
   ```
   Please integrate this into the handbook:
   [Your knowledge: article, code, experience, etc.]
   ```

   The agent follows a defined integration protocol (see [AGENTS.md](AGENTS.md)):
   - Classifies the knowledge by type and location
   - Searches existing docs before creating anything new
   - Updates existing documents where appropriate
   - Creates new content only if no suitable home exists
   - Cross-links everything to related processes and patterns
   - Validates structure, navigation, and consistency

3. **Review and approve the changes**
   - The agent shows you what it changed (Git diff)
   - You can request modifications or reject
   - Approved changes are merged

### For Humans: Knowledge Integration Process

The handbook uses AI-assisted knowledge integration. When you provide new knowledge:

1. **Classification** — What type is this knowledge? (Pattern? Adapter? Example? Etc.)
2. **Search** — Does it already exist in the handbook?
3. **Integration** — Add/update/enrich appropriate handbook documents
4. **Cross-Linking** — Connect to related processes, patterns, concepts
5. **Validation** — Verify structure, navigation, and quality

See [system/authoring/README.md](system/authoring/README.md) for details.

### For AI Agents: How to Maintain the Handbook

👉 **See [AGENTS.md](AGENTS.md) for complete maintenance and operational guidance.**

---

## Handbook Development

See the handbook's development issues in [GitHub Issues](https://github.com/rob-bl8ke/ai-assisted-engineering-handbook/issues).

The handbook is designed to evolve through:

1. Practical documentation of working approaches
2. Gradual organization and taxonomy refinement
3. Reuse and cross-linking to eliminate duplication
4. AI-assisted maintenance and evolution

To propose changes or additions:

- **For simple ideas:** Open an issue describing the engineering situation, category, and how it relates to existing concepts
- **For implementation contributions:** Provide code, scripts, or working examples
- **For articles/research:** Share URLs or material; ask agent to integrate
- **For feedback:** Describe what's missing or unclear and suggest improvements

---

## Key References

- **PRD.md** — Full product requirements, design principles, and initial process model
- **GitHub Issues** — Development roadmap and specification details

---

**Last Updated:** 2026-08-16  
**Status:** Handbook Bootstrap (Navigation, Process, Core Patterns, and Playbooks Documented)
