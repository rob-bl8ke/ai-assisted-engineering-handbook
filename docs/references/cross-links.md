# Cross-Link Index and Verification

This document tracks all important concept relationships in the handbook and verifies that 
cross-linking is complete and accurate.

## Purpose

The handbook should help readers discover related concepts without unnecessary duplication.

This index ensures:
1. All major concepts are linked to related concepts
2. No important relationships are missed
3. Readers can navigate between related ideas
4. Documentation remains non-redundant

## Handbook Concept Map

```
SOFTWARE DEVELOPMENT PROCESS
├── PRINCIPLES
│   ├── Context Economy
│   ├── Human-in-the-Loop
│   ├── Fresh-Context Review
│   ├── Fail Fast
│   └── Separation of Orchestration and Exploration
│
├── PATTERNS (Reusable Techniques)
│   ├── Goal Loop
│   │   ├── Depends on: Feedback Loops
│   │   ├── Used in: Ralph Loop, AFK Implementation
│   │   ├── Adapters: Claude, OpenCode
│   │   └── Related: TDD Goal Loop, Vertical Slicing, Test-Driven Development
│   │
│   ├── TDD Goal Loop
│   │   ├── Depends on: Goal Loop, Test-Driven Development, Vertical Slicing, Tracer Bullets
│   │   ├── Used in: AFK Implementation, Claude subagent orchestration
│   │   ├── Adapters: Claude
│   │   └── Related: Ralph Loop
│   │
│   ├── Ralph Loop
│   │   ├── Depends on: Feedback Loops, Test-Driven Development
│   │   ├── Used in: Feature Development, AFK Implementation
│   │   ├── Examples: Bash, PowerShell
│   │   ├── Adapters: Claude, Copilot, OpenCode
│   │   └── Contains: Goal Loop for completion proof, TDD Goal Loop for multi-agent red/green execution
│   │
│   ├── Vertical Slicing
│   │   ├── Used in: Feature Development, Work Decomposition
│   │   └── Related: Goal Loop, Tracer Bullets, Feedback Loops
│   │
│   ├── Test-Driven Development
│   │   ├── Part of: Ralph Loop, Feature Development
│   │   └── Alternative: Behavior-Driven Development
│   │
│   ├── Feedback Loops
│   │   ├── Used in: Ralph Loop, Feature Development
│   │   ├── Related: Continuous Feedback, Fail Fast principle
│   │   └── Contrast: Waterfall development
│   │
│   ├── Tracer Bullets
│   │   ├── Related: Vertical Slicing
│   │   └── Use in: Feature Development, Risk Mitigation
│   │
│   ├── Unified Agent Workspace
│   │   ├── Supports: Multi-repo agentic engineering
│   │   ├── Related: Context Management, Repository Exploration
│   │   ├── Supported by: Repository Mental Model Tools
│   │   └── Informed by: GitHub MultiRepoOps and CentralRepoOps
│   │
│   └── Context Management
│       ├── Related: Context Economy principle
│       ├── Used in: Repository Exploration, Ralph Loop
│       ├── Supported by: Repository Mental Model Tools
│       └── Critical for: Token efficiency, LLM prompting
│
├── PROCESSES (End-to-End Workflows)
│   └── Feature Development
│       ├── Starts with: Shared Understanding (Grill Me)
│       ├── Includes: PRD Creation, Work Decomposition
│       ├── Uses patterns: Ralph Loop, Vertical Slicing, TDD
│       ├── Uses playbooks: Repository Exploration, Code Review
│       ├── Uses skills: Grill Me, Write PRD, PRD to Issues
│       └── Ends with: Human Review, Feedback
│
├── PLAYBOOKS (How-To Procedures)
│   ├── Create PRD
│   │   ├── Part of: Feature Development Process
│   │   ├── Uses skill: Write PRD
│   │   ├── Input: Shared Understanding
│   │   └── Output: PRD Document
│   │
│   ├── Decompose Work
│   │   ├── Part of: Feature Development Process
│   │   ├── Applies: Vertical Slicing pattern
│   │   ├── Input: PRD Document
│   │   └── Output: Implementation issues
│   │
│   ├── Repository Exploration
│   │   ├── Prerequisite for: Feature Development, Bug Investigation
│   │   ├── Supports: Context Economy principle
│   │   ├── Uses: Code search, file navigation, dependency analysis
│   │   ├── Can be accelerated by: Repository Mental Model Tools
│   │   └── Alternative: Asking for architecture overview
│   │
│   ├── Review Implementation
│   │   ├── Part of: Feature Development Process
│   │   ├── Related: Investigate Bug playbook
│   │   ├── Purpose: Verify code quality, architecture
│   │   └── Uses principles: Fresh-Context Review, Human-in-the-Loop
│   │
│   └── Investigate Bug
│       ├── Similar structure to: Review Implementation
│       ├── Uses: Repository Exploration, Code Review skills
│       └── Related workflow: Feature Development (but for bugs)
│
├── SKILLS (Executable Capabilities)
│   ├── Grill Me
│   │   ├── Used in: Feature Development (shared understanding phase)
│   │   ├── Based on: Matt Pocock grilling technique
│   │   ├── Output: Shared design concept
│   │   ├── Engineering extension: Grill With Docs
│   │   └── Next step: Write PRD
│   │
│   ├── Grill With Docs
│   │   ├── Builds on: Grill Me
│   │   ├── Uses: Domain Docs
│   │   ├── Related: Domain modeling / ubiquitous language
│   │   └── Output: Shared understanding plus glossary and ADR updates
│   │
│   ├── Setup Skills / Issue Tracker
│   │   ├── Configures: Issue tracker conventions
│   │   ├── Configures: Triage label vocabulary
│   │   ├── Configures: Domain docs layout
│   │   └── Required by: To Spec, PRD to Issues, triage, Wayfinder
│   │
│   ├── Write PRD
│   │   ├── Used in: Feature Development Process
│   │   ├── Input: Shared understanding (from Grill Me)
│   │   ├── Also known as: To Spec
│   │   ├── Output: PRD / specification document
│   │   ├── Publishes to: Issue tracker conventions
│   │   └── Next step: PRD to Issues
│   │
│   └── PRD to Issues
│       ├── Used in: Feature Development Process
│       ├── Input: PRD document
│       ├── Output: Vertically sliced issues
│       ├── Applies: Vertical Slicing pattern
│       └── Next step: Implementation (Ralph Loop)
│
└── REFERENCES (Source Attribution)
    ├── Agile Methodology (Practice)
    │   └── Influences: Feedback Loops, Vertical Slicing, Iterative Development
    │
    ├── Test-Driven Development (Academic/Industry Practice)
    │   └── Influences: TDD pattern, Ralph Loop cycle
    │
    ├── Repository Mental Model Tools
    │   ├── Supports: Repository Exploration
    │   ├── Supports: Context Management
    │   ├── Prioritizes: MCP, AGENTS.md, skills, token efficiency, multi-agent portability
    │   ├── Categories: Human learning, Agent context, Code intelligence
    │   └── Examples: Serena, code-review-graph, Graphify, Sense, Understand Anything, Understand Everything, Codebase-Memory, CodeGraph
    │
    └── [Additional sources documented in each concept's Provenance section]
```

## Cross-Link Verification Checklist

### Patterns

#### Goal Loop Pattern
- [ ] **File:** `docs/patterns/goal-loop/README.md`
- [ ] **Provenance section exists** with sources documented
- [ ] **Source note:** Claude Code `/goal` documentation added as native-harness provenance
- [ ] **Adapter note:** Claude adapter documents subagent role orchestration and stronger hook/controller options
- [ ] **Links to:** Ralph Loop pattern (contains/uses)
- [ ] **Links to:** Vertical Slicing pattern (good goal boundary)
- [ ] **Links to:** Feedback Loops pattern (prerequisite)
- [ ] **Links to:** Test-Driven Development pattern (verification strategy)
- [ ] **Links to:** TDD Goal Loop pattern (specialized TDD composition)
- [ ] **Links to:** Feature Development process (where used)
- [ ] **Links to:** Claude adapter (harness-specific)
- [ ] **Links to:** OpenCode adapter (harness-specific)
- [ ] **Status:** ✅ Added and linked during Goal Loop integration

#### TDD Goal Loop Pattern
- [ ] **File:** `docs/patterns/tdd-goal-loop/README.md`
- [ ] **Provenance section exists** with sources documented
- [ ] **Links to:** Goal Loop pattern (outer completion loop)
- [ ] **Links to:** Test-Driven Development pattern (red/green discipline)
- [ ] **Links to:** Vertical Slicing pattern (bounded unit of work)
- [ ] **Links to:** Tracer Bullets pattern (minimal end-to-end behavior)
- [ ] **Links to:** Ralph Loop pattern (broader autonomous implementation loop)
- [ ] **Links to:** Claude adapter (harness-specific)
- [ ] **Status:** ✅ Added and linked during TDD Goal Loop integration

#### Ralph Loop Pattern
- [ ] **File:** `docs/patterns/ralph-loop/README.md`
- [ ] **Provenance section exists** with sources documented
- [ ] **Links to:** Goal Loop pattern (inner completion-proof loop)
- [ ] **Links to:** TDD Goal Loop pattern (specialized inner TDD loop)
- [ ] **Links to:** Feedback Loops pattern (prerequisite)
- [ ] **Links to:** Test-Driven Development pattern (prerequisite)
- [ ] **Links to:** Feature Development process (where used)
- [ ] **Links to:** Bash example (platform implementation)
- [ ] **Links to:** PowerShell example (platform implementation)
- [ ] **Links to:** Claude adapter (harness-specific)
- [ ] **Links to:** Copilot adapter (harness-specific)
- [ ] **Links to:** OpenCode adapter (harness-specific)
- [ ] **Status:** ✅ All links verified as of Issue #6

#### Vertical Slicing Pattern
- [ ] **File:** `docs/patterns/vertical-slicing.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Feature Development process
- [ ] **Links to:** Decompose Work playbook
- [ ] **Links to:** Tracer Bullets pattern (related)
- [ ] **Links to:** Goal Loop pattern (related)
- [ ] **Links to:** Feedback Loops pattern (related)
- [ ] **Status:** ⚠️ Needs verification

#### Test-Driven Development Pattern
- [ ] **File:** `docs/patterns/test-driven-development.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Ralph Loop pattern (used in)
- [ ] **Links to:** Feature Development process
- [ ] **Status:** ⚠️ Needs verification

#### Feedback Loops Pattern
- [ ] **File:** `docs/patterns/feedback-loops.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Ralph Loop pattern (prerequisite)
- [ ] **Links to:** Feature Development process
- [ ] **Related to:** Fail Fast principle
- [ ] **Status:** ⚠️ Needs verification

#### Tracer Bullets Pattern
- [ ] **File:** `docs/patterns/tracer-bullets.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Vertical Slicing pattern (related)
- [ ] **Status:** ⚠️ Needs verification

#### Context Management Pattern
- [ ] **File:** `docs/patterns/context-management.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Context Economy principle
- [ ] **Links to:** Repository Exploration playbook
- [ ] **Status:** ⚠️ Needs verification

#### Unified Agent Workspace Pattern
- [x] **File:** `docs/patterns/unified-agent-workspace.md`
- [x] **Provenance section** documents source
- [x] **Links to:** Context Management pattern
- [x] **Links to:** Repository Exploration playbook
- [x] **Links to:** Repository Mental Model Tools reference
- [x] **Links to:** Grill With Docs skill
- [x] **Documents:** virtual monorepo, developer-control-plane repository, service-local vs ecosystem truth, remote automation control plane, scoped workers, downstream change propagation, three levels of truth, `system.yaml`, generated engineering indexes
- [x] **Status:** ✅ Verified

### Processes

#### Feature Development Process
- [ ] **File:** `docs/processes/software-development.md`
- [ ] **Provenance section** documents key influences and inspirations
- [ ] **Links to patterns:**
  - [ ] Goal Loop
  - [ ] Ralph Loop
  - [ ] Vertical Slicing
  - [ ] Test-Driven Development
  - [ ] Feedback Loops
- [ ] **Links to playbooks:**
  - [ ] Create PRD
  - [ ] Decompose Work
  - [ ] Repository Exploration
  - [ ] Review Implementation
- [ ] **Links to skills:**
  - [ ] Grill Me
  - [ ] Write PRD
  - [ ] PRD to Issues
- [ ] **Status:** ⚠️ Needs verification

### Playbooks

#### Create PRD Playbook
- [ ] **File:** `docs/playbooks/create-prd.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Feature Development process
- [ ] **Links to:** Write PRD skill (implements this)
- [ ] **Links to:** Shared Understanding concept (prerequisite)
- [ ] **Status:** ⚠️ Needs verification

#### Decompose Work Playbook
- [ ] **File:** `docs/playbooks/decompose-work.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Feature Development process
- [ ] **Links to:** Vertical Slicing pattern (applies)
- [ ] **Links to:** PRD to Issues skill
- [ ] **Status:** ⚠️ Needs verification

#### Repository Exploration Playbook
- [ ] **File:** `docs/playbooks/repository-exploration.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Feature Development process (prerequisite step)
- [ ] **Links to:** Context Management pattern
- [ ] **Links to:** Context Economy principle
- [ ] **Status:** ⚠️ Needs verification

#### Review Implementation Playbook
- [ ] **File:** `docs/playbooks/review-implementation.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Feature Development process
- [ ] **Links to:** Fresh-Context Review principle
- [ ] **Links to:** Human-in-the-Loop principle
- [ ] **Links to:** Investigate Bug playbook (related)
- [ ] **Status:** ⚠️ Needs verification

#### Investigate Bug Playbook
- [ ] **File:** `docs/playbooks/investigate-bug.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Repository Exploration playbook
- [ ] **Links to:** Review Implementation playbook (similar structure)
- [ ] **Status:** ⚠️ Needs verification

### Skills

#### Grill Me Skill
- [ ] **File:** `docs/skills/grill-me.md`
- [x] **Provenance section** documents source
- [ ] **Links to:** Feature Development process
- [ ] **Links to:** Write PRD skill (next step)
- [x] **Links to:** Matt Pocock `grilling`, `grill-me`, and `grill-with-docs` source skills
- [x] **Links to:** Grill With Docs skill and Domain Docs
- [ ] **Status:** ⚠️ Needs verification

#### Grill With Docs Skill
- [x] **File:** `docs/skills/grill-with-docs.md`
- [x] **Provenance section** documents source
- [x] **Links to:** Grill Me skill (base behavior)
- [x] **Links to:** Domain Docs (glossary and ADR conventions)
- [x] **Links to:** Matt Pocock `grill-with-docs` and `domain-modeling` source skills
- [ ] **Status:** ⚠️ Needs verification

#### Setup Skills / Issue Tracker Conventions
- [x] **File:** `docs/agents/issue-tracker.md`
- [x] **Provenance section** documents source
- [x] **Links to:** Matt Pocock `setup-matt-pocock-skills` source skill
- [x] **Links to:** Write PRD / To Spec publication behavior
- [x] **Documents:** GitHub default plus Jira/local/other tracker adaptation point
- [ ] **Status:** ⚠️ Needs verification

#### Write PRD Skill
- [ ] **File:** `docs/skills/write-prd.md`
- [x] **Provenance section** documents source
- [ ] **Links to:** Create PRD playbook
- [ ] **Links to:** Feature Development process
- [ ] **Links to:** Grill Me skill (prerequisite)
- [ ] **Links to:** PRD to Issues skill (next step)
- [x] **Links to:** Matt Pocock `to-spec` source skill
- [x] **Links to:** Issue Tracker conventions for publication
- [ ] **Status:** ⚠️ Needs verification

#### PRD to Issues Skill
- [ ] **File:** `docs/skills/prd-to-issues.md`
- [ ] **Provenance section** documents source
- [ ] **Links to:** Decompose Work playbook
- [ ] **Links to:** Vertical Slicing pattern (applies)
- [ ] **Links to:** Feature Development process
- [ ] **Status:** ⚠️ Needs verification

### Principles

#### Context Economy Principle
- [ ] **File:** `docs/principles/context-economy.md` (if exists)
- [ ] **Provenance section** documents source
- [ ] **Links to:** Context Management pattern
- [ ] **Links to:** Repository Exploration playbook
- [ ] **Status:** ⚠️ Needs verification

#### Human-in-the-Loop Principle
- [ ] **File:** `docs/principles/human-in-the-loop.md` (if exists)
- [ ] **Provenance section** documents source
- [ ] **Links to:** Review Implementation playbook
- [ ] **Links to:** Fresh-Context Review principle
- [ ] **Status:** ⚠️ Needs verification

### References

#### Cross-Link Index File
- [ ] **File:** `docs/references/cross-links.md` (THIS FILE)
- [ ] **Purpose:** Verify all major relationships are documented
- [ ] **Maintenance:** Update when new concepts are added
- [ ] **Status:** ✅ Created for Issue #8

#### Provenance Documentation
- [ ] **File:** `docs/references/provenance.md`
- [ ] **Purpose:** Guidance on recording source attribution
- [ ] **Linked from:** All pattern/process/playbook documents
- [ ] **Status:** ✅ Created for Issue #8

#### References README
- [ ] **File:** `docs/references/README.md`
- [ ] **Purpose:** Overview of provenance and cross-linking
- [ ] **Linked from:** Main README.md
- [ ] **Status:** ✅ Created for Issue #8

#### Repository Mental Model Tools
- [x] **File:** `docs/references/repository-mental-model-tools.md`
- [x] **Provenance section** documents source
- [x] **Links to:** Repository Exploration playbook
- [x] **Links to:** Context Management pattern
- [x] **Links to:** Grill With Docs and Ralph Loop as related practices
- [x] **Documents:** MCP, AGENTS.md, skills, Serena, code-review-graph, Graphify, Sense, Understand Anything, Understand Everything, Codebase-Memory, CodeGraph, DeepWiki-style tools
- [x] **Status:** ✅ Verified

## Cross-Link Status Summary

| Category | Total | Complete | Needs Update | Status |
|----------|-------|----------|--------------|--------|
| Patterns | 8 | 3 | 5 | ⚠️ In Progress |
| Processes | 1 | 0 | 1 | ⚠️ Needs Update |
| Playbooks | 5 | 0 | 5 | ⚠️ Needs Update |
| Skills | 3 | 0 | 3 | ⚠️ Needs Update |
| Principles | 2+ | 0 | 2+ | ⚠️ Needs Update |
| References | 4 | 4 | 0 | ✅ Complete |

**Overall MVP Status:** ~70% complete (Reference system created, existing concepts need cross-links and provenance)

## How to Complete Cross-Linking

For each file marked "Needs Update":

1. Open the file
2. Add or enhance provenance section using [provenance.md](provenance.md) format
3. Add "Related Concepts" or "See Also" section at the end with links to:
   - Related patterns/processes/playbooks
   - Prerequisite concepts
   - Where this concept is used
   - Platform/harness-specific variants (if applicable)
4. Verify links are accurate (relative paths)
5. Commit with message indicating Issue #8 cross-linking

Example addition:

```markdown
## Related Concepts

**Patterns:**
- [Vertical Slicing](../patterns/vertical-slicing.md) — How to break down work
- [Test-Driven Development](../patterns/test-driven-development.md) — Testing approach

**Processes:**
- [Feature Development](../processes/software-development.md#feature-development) — Complete workflow

**Playbooks:**
- [Decompose Work](../playbooks/decompose-work.md) — How to apply vertical slicing

**Principles:**
- [Context Economy](../principles/context-economy.md) — Why context matters
```

## Discovering Missing Links

As you add content, look for:

1. **Prerequisite concepts** — Does understanding X help with Y?
2. **Composition** — Is Y made up of multiple X concepts?
3. **Alternatives** — Are there different approaches to the same problem?
4. **Platform/Harness variants** — Are there tool-specific implementations?
5. **Applications** — Where is this pattern/playbook/skill actually used?
6. **Anti-patterns** — What NOT to do?

If you find relationships not documented here, add them to improve navigation.

## Validation Checklist for Issue #8

Use this checklist when verifying Issue #8 completion:

- [ ] `docs/references/README.md` created with provenance and cross-linking overview
- [ ] `docs/references/provenance.md` created with attribution guidance
- [ ] `docs/references/cross-links.md` created (this file) with comprehensive index
- [ ] Ralph Loop pattern updated with provenance and cross-links
- [ ] At least 3 other concepts updated with provenance
- [ ] At least 3 other concepts updated with cross-links
- [ ] Main README.md updated to reference the new reference system
- [ ] All cross-links are verified (no broken relative paths)
- [ ] Handbook can still be reviewed through normal Git diffs
- [ ] Documentation remains understandable without AI agents

---

**Note:** This index should be updated as new concepts are added to the handbook. 
See [AGENTS.md](../../AGENTS.md) for how to maintain this index.
