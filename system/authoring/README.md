# Handbook Authoring Guide

**Audience:** AI agents maintaining the handbook; humans contributing knowledge  
**Purpose:** Explain how to integrate new knowledge into the AI-Assisted Engineering Handbook  
**Related:** [AGENTS.md](../../AGENTS.md) (quick reference), [classify.md](classify.md), [integrate.md](integrate.md), [validate.md](validate.md)

---

## Overview

This guide explains the complete workflow for integrating new knowledge into the handbook.

The workflow is structured in four phases:

1. **Classify** — Determine what type(s) of knowledge you have
2. **Integrate** — Add or update handbook documents
3. **Cross-Link** — Connect related concepts
4. **Validate** — Verify structure and navigation remain correct

---

## Phase 1: Classify New Knowledge

**Goal:** Understand what you're dealing with

**Input:** New information (article, implementation, idea, conversation, etc.)  
**Output:** Classification of the knowledge type(s)

### Knowledge Categories

| Category | Definition | Examples |
|----------|-----------|----------|
| **Principle** | Underlying reasoning; foundational idea | Context economy, human-in-the-loop, fail-fast |
| **Pattern** | Reusable technique appearing in multiple contexts | Ralph Loops, vertical slicing, TDD, tracer bullets |
| **Process** | End-to-end workflow for a major activity | Feature development, bug fixing, repository exploration |
| **Playbook** | Step-by-step procedure for a specific task | How to review code, how to write a PRD, how to investigate a bug |
| **Skill** | Executable AI capability implementing a pattern | Grill Me (discovery), Write PRD (specification), Code Reviewer (review) |
| **Scenario** | Entry point based on developer's situation | "I have a vague feature idea", "I inherited an unfamiliar codebase" |
| **Adapter** | Harness-specific guidance (Claude, Copilot, OpenCode) | How a pattern works with Claude's architecture, Copilot's chat interface |
| **Example** | Platform-specific implementation (Bash, PowerShell, Python) | Ralph Loop in Bash vs. PowerShell, context management on Windows vs. macOS |
| **Reference** | Provenance; where an idea originated | Blog posts, videos, papers, practitioners, conversations |

### Classification Process

Ask these questions:

1. **Does this explain a core idea?** → Principle
2. **Does this show a reusable technique?** → Pattern
3. **Does this describe a workflow sequence?** → Process
4. **Does this provide step-by-step guidance?** → Playbook
5. **Can this be implemented as AI automation?** → Skill
6. **Does this address a specific situation?** → Scenario
7. **Is this specific to Claude/Copilot/OpenCode?** → Adapter
8. **Is this specific to Bash/PowerShell/platform?** → Example
9. **Does this cite or reference source material?** → Reference

Multiple categories may apply.

### Classification Example

**Input:** "Here's a PowerShell script that implements Ralph Loops with Claude"

**Analysis:**
- Pattern? YES — This is a concrete implementation of the Ralph Loop pattern
- Adapter? YES — It's specific to Claude
- Example? YES — It's specific to PowerShell
- Reference? MAYBE — Document the source/author

**Classification:** Pattern (Ralph Loop) + Adapter (Claude) + Example (PowerShell) + Reference (if applicable)

See [classify.md](classify.md) for detailed classification guidance.

---

## Phase 2: Integrate Into Handbook

**Goal:** Add or update handbook documents with the new knowledge

**Principle:** Prefer enriching existing documents over creating new ones

### Integration Strategy

Before creating a new document:

```
1. Search existing handbook
2. Check if concept already exists
3. If YES → Update existing document
4. If NO → Check if this is a variant of existing concept
5. If variant → Add to /examples/ or /adapters/
6. If truly new → Create new document (rare)
```

### Integration Examples

#### Example 1: Adding a new adapter

**Input:** "I've tried Ralph Loop with OpenCode and here's how it works"

**Classification:** Pattern + Adapter + Example

**Integration Strategy:**
- Check if `docs/patterns/ralph-loop/` exists → YES
- Check if `docs/patterns/ralph-loop/adapters/opencode.md` exists → NO
- Action: CREATE `docs/patterns/ralph-loop/adapters/opencode.md`
- Update `docs/patterns/ralph-loop/README.md` to reference new adapter
- Link from process if applicable

#### Example 2: Enhancing a process

**Input:** "Here's an additional rainy path we discovered: what if the test framework isn't installed?"

**Classification:** Process + Playbook

**Integration Strategy:**
- Find affected process (likely `docs/processes/software-development.md`)
- Locate relevant phase (likely Phase 4: Implementation)
- Add rainy path section documenting the blocker and recovery
- Do NOT create a new file

#### Example 3: Supporting a pattern with implementation

**Input:** "Here's how context management is done in PowerShell"

**Classification:** Pattern + Example (platform-specific)

**Integration Strategy:**
- Check if `docs/patterns/context-management/` exists → YES
- Check if `docs/patterns/context-management/examples/powershell.md` exists → NO
- Action: CREATE file and add PowerShell-specific implementation
- Ensure core concept (`README.md`) explains context management without platform assumptions
- Do NOT create "context-management-powershell.md" at root level

See [integrate.md](integrate.md) for detailed integration guidance.

---

## Phase 3: Cross-Link Related Concepts

**Goal:** Connect the new knowledge to existing processes, patterns, and skills

### Cross-Linking Checklist

After adding/updating a document:

- [ ] Does any **process** reference this concept? → Add "See Also" link
- [ ] Does any **pattern** reference this concept? → Add "Related Patterns" section
- [ ] Does any **playbook** use this technique? → Add reference or link
- [ ] Does any **skill** operationalize this concept? → Cross-reference
- [ ] Are there **related patterns**? → Ensure they link to each other
- [ ] Does this affect **navigation**? → Update README or index
- [ ] Should **scenarios** link here? → Add reference

### Cross-Linking Example

**Scenario:** You've added a new adapter `docs/patterns/ralph-loop/adapters/opencode.md`

**Cross-links to add:**

1. `docs/patterns/ralph-loop/README.md` → Add OpenCode adapter to "Harness-Specific Implementations" section
2. `docs/processes/software-development.md` Phase 4.2 → Add reference to Ralph Loop pattern
3. `README.md` → If Ralph Loop is in Quick Navigation, ensure it's current
4. Related adapter files (`claude.md`, `copilot.md`) → Add cross-reference

See [validate.md](validate.md) for cross-link validation.

---

## Phase 4: Validate Changes

**Goal:** Verify the handbook remains coherent and navigable

### Validation Checklist

- [ ] **Navigation:** Can a reader still find what they need?
- [ ] **Links:** Do all cross-references work? (No broken links)
- [ ] **Structure:** Does the document hierarchy make sense?
- [ ] **Separation:** Are concepts separate from tool/platform specifics?
- [ ] **Completeness:** Is there anything incomplete or TODO left behind?
- [ ] **Consistency:** Does new content match style and structure of existing docs?
- [ ] **Duplication:** Is any knowledge now documented in multiple places?
- [ ] **Provenance:** Is the source documented if applicable?

See [validate.md](validate.md) for detailed validation guidance.

---

## Knowledge Integration Workflow (Complete)

Here's the full workflow from start to finish:

```
NEW KNOWLEDGE (article, implementation, idea, conversation)
        │
        ▼
    INGEST
    Read and understand the source material
        │
        ▼
    CLASSIFY
    Determine knowledge type(s)
    └─ Principle? Pattern? Process? Playbook? Skill? Scenario?
       Adapter? Example? Reference?
        │
        ▼
    SEARCH
    Check if this concept exists in handbook
    └─ Query handbook by keyword and category
        │
        ├─ FOUND (concept already exists)
        │  ├─ Exact match? → ENRICH (update existing)
        │  └─ Related concept? → CROSS-LINK and possibly extend
        │
        └─ NOT FOUND (concept is new)
           ├─ Is this a variant? → CREATE in /examples/ or /adapters/
           └─ Is this standalone? → CREATE new document
        │
        ▼
    INTEGRATE
    Add or update handbook documents
    └─ Create file or update existing content
        │
        ▼
    CROSS-LINK
    Connect to related concepts
    └─ Update README references, See Also sections, Related Patterns
        │
        ▼
    VALIDATE
    Verify structure and navigation
    └─ Check links, structure, duplication, separation of concerns
        │
        ▼
    REPORT
    Summarize changes for human maintainer
    └─ Files added/updated, changes made, cross-links verified
```

---

## Authoring Principle: Enrich Before Creating

The single most important principle:

> **Prefer enriching and updating existing documentation over creating new documents.**

This prevents:
- Duplication (same concept documented multiple places)
- Fragmentation (related knowledge scattered across files)
- Navigation problems (too many files confuses readers)
- Maintenance burden (changes must be made in multiple places)

### Decision Tree: Create or Enrich?

```
Is this knowledge already documented somewhere?
├─ YES
│  └─ ENRICH existing document
│     └─ Add missing information
│     └─ Include new example
│     └─ Add cross-link
│     └─ Update rainy path
│
└─ NO
   ├─ Is this a tool-specific (Claude, Copilot, OpenCode)?
   │  └─ YES → CREATE in /adapters/
   │
   ├─ Is this a platform-specific (Bash, PowerShell)?
   │  └─ YES → CREATE in /examples/
   │
   └─ Is this a new standalone concept?
      └─ YES → CREATE new document
         (but verify first that it's truly new!)
```

---

## Separation of Concerns: Three Layers

The handbook maintains three distinct layers:

### Layer 1: Canonical Concept
**Tool/platform agnostic explanation**

Location: `docs/patterns/[name]/README.md` or `docs/processes/[name].md`

Example:
```markdown
# Ralph Loop Pattern

## Core Concept

A Ralph Loop is an autonomous agent feedback loop where:
1. Agent receives specification
2. Agent implements using TDD
3. Agent commits with clear messages
4. Agent encounters blocker → Pauses
5. Human clarifies → Agent resumes
...
```

No tool names. No platform-specific code. Pure concept.

### Layer 2: Implementation Examples
**Platform-specific (Bash, PowerShell, Python)**

Location: `docs/patterns/[name]/examples/[platform].md`

Example:
```markdown
# Ralph Loop: Bash Implementation

## The Script: ralph-loop.sh

#!/bin/bash
# Claude-specific call...
```

Shows concrete Bash code implementing the pattern.

### Layer 3: Harness Adapters
**AI tool-specific guidance (Claude, Copilot, OpenCode)**

Location: `docs/patterns/[name]/adapters/[harness].md`

Example:
```markdown
# Ralph Loop: Claude Adapter

## Claude API Configuration

CLAUDE_API_URL="https://api.anthropic.com/v1/messages"
CLAUDE_MODEL="claude-3-5-sonnet-20241022"
```

Shows how Claude differs from other AI tools.

---

## Document Structure Template

### Pattern Document

```markdown
# [Pattern Name] Pattern

**Status:** [Mature/Beta/Experimental]  
**Related Patterns:** [Link], [Link]  
**Operationalizes:** [Which process phase?]

## Core Concept
[Tool-agnostic explanation]

## Core Decision Points
[If/then logic]

## Decision Flow
[Diagram or flowchart]

## Canonical Behavior
[Step-by-step procedure independent of tools]

## Verification Checklist
[Observable, testable outcomes]

## Common Pitfalls
[What goes wrong and how to avoid it]

## See Also
[Related patterns, playbooks, processes]

## Examples
[Links to platform examples]

## Adapters
[Links to harness adapters]
```

### Process Document

```markdown
# [Process Name]

**Goal:** [What should the developer achieve?]

## Overview
[High-level workflow]

## Phases

### Phase 1: [Name]
[Steps and approach]

### Phase 2: [Name]
[Steps and approach]

## Rainy Paths
[What can go wrong and how to recover]

## Validation
[How to know when complete]

## See Also
[Related processes, patterns, playbooks]
```

### Playbook Document

```markdown
# [Playbook Name]

**Purpose:** [What task does this guide?]  
**Related Pattern:** [Which pattern does this operationalize?]

## When to Use This Playbook
[Situation description]

## Step-by-Step Procedure
1. [Step]
2. [Step]
...

## Verification
[How to know you completed it correctly]

## Common Mistakes
[What goes wrong]

## See Also
[Related playbooks, patterns]
```

---

## Authoring Checklist

Before marking a document complete:

### Content Quality
- [ ] Concept is explained clearly and concisely
- [ ] Examples are complete and runnable
- [ ] Cross-links point to existing documents
- [ ] Tool/platform assumptions are explicit or absent
- [ ] Rainy paths are documented

### Structure
- [ ] Document follows appropriate template
- [ ] Headings are clear and hierarchical
- [ ] Lists are properly formatted
- [ ] Code blocks are properly tagged with language

### Navigation
- [ ] Document is referenced from appropriate index/navigation
- [ ] All cross-links are updated
- [ ] README mentions this concept if it's important

### Separation of Concerns
- [ ] Canonical version has no tool/platform specific content
- [ ] Tool-specific guidance is in `/adapters/`
- [ ] Platform-specific code is in `/examples/`
- [ ] Related concepts are linked, not duplicated

---

## Special Cases

### When Something Is Tool-Specific

**Example:** "Claude has a 200K context window; Copilot doesn't"

**Don't:** Add this to the canonical concept  
**Do:** Create or update the adapter document
```
docs/patterns/context-management/adapters/claude.md
docs/patterns/context-management/adapters/copilot.md
```

Then reference adapters from core concept:

```markdown
## Harness Differences

See tool-specific notes:
- [Claude Adapter](adapters/claude.md)
- [Copilot Adapter](adapters/copilot.md)
```

### When Something Is Platform-Specific

**Example:** "PowerShell uses $VAR but Bash uses $VAR"

**Don't:** Add complex shell conditionals to examples  
**Do:** Create separate example files
```
docs/patterns/some-pattern/examples/bash.md
docs/patterns/some-pattern/examples/powershell.md
```

Then reference from core concept:

```markdown
## Platform Examples

- [Bash/Unix Implementation](examples/bash.md)
- [PowerShell/Windows Implementation](examples/powershell.md)
```

### When Knowledge Conflicts with Existing Documentation

**Example:** "I tried this pattern and it didn't work for me because X"

**Action:**
1. Add to "Common Pitfalls" section of relevant pattern
2. Document the blocker and workaround
3. Update rainy path if applicable
4. Preserve original guidance unless proven incorrect

**Example text:**
```markdown
## Pitfall: [Name]

**Problem:** [What goes wrong]

**Example:** [When this happened]

**Solution:** [How to fix it]
```

---

## Human Integration: Contributing to the Handbook

For humans who want to contribute:

### Process

1. **Identify what you want to contribute**
   - An idea
   - A working implementation
   - A blog post or article
   - An experience or lesson learned
   - A new tool or technique

2. **Ask an AI agent to integrate it**
   ```
   Please integrate this into the handbook:
   [Your input]
   
   Classify it, search for related concepts, 
   update existing docs where appropriate,
   and cross-link everything.
   ```

3. **Review the changes**
   - The agent will show you what it changed (Git diff)
   - You can approve, request modifications, or reject
   - Changes are merged into the main handbook

4. **Trust the agent's judgment... but verify**
   - The agent has been trained in handbook structure
   - But you are the final decision-maker
   - If something doesn't fit, it's not too late to modify

### Communication Tips

**Be specific:**
```
✓ "Here's a Bash script for Ralph Loop with OpenCode"
✗ "I did some AI stuff that works"
```

**Provide context:**
```
✓ "This is Claude-specific because it uses the API directly"
✗ "Claude works different"
```

**Suggest where it belongs (optional):**
```
✓ "I think this is an adapter for the Ralph Loop pattern"
✗ "Put this somewhere"
```

**Give the agent room to search:**
```
✓ "Integrate this into the handbook, checking for existing docs first"
✗ "Create a new file called X.md"
```

---

## See Also

- **[AGENTS.md](../../AGENTS.md)** — Quick reference for AI agent instructions
- **[classify.md](classify.md)** — Detailed classification guidance
- **[integrate.md](integrate.md)** — Detailed integration guidance
- **[validate.md](validate.md)** — Detailed validation guidance
- **[../../README.md](../../README.md)** — Handbook navigation and overview

