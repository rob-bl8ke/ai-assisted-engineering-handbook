# Integration Guide

**Purpose:** How to integrate classified knowledge into the handbook  
**Audience:** AI agents adding content; humans guiding integration  
**Prerequisite:** [classify.md](classify.md) — Classification complete  
**Next:** [validate.md](validate.md) — Validation after integration

---

## Core Principle

> **Prefer enriching existing documentation over creating new documents.**

Before creating anything new, ask:

1. **Does this concept already exist?** → ENRICH
2. **Is this a variant of existing concept?** → ADD TO VARIANTS (examples/adapters)
3. **Is this truly new?** → CREATE (rarely needed)

---

## Integration Strategy: Decision Tree

```
You have classified knowledge. Now integrate it:

┌──────────────────────────────────────────────────┐
│ Does this concept already exist in handbook?     │
└──────────────────────────────────────────────────┘
         │
         ├─ YES → Go to ENRICH section
         │
         └─ NO ↓
            │
            ├─ Is this tool-specific (Claude/Copilot/OpenCode)?
            │  └─ YES → CREATE in /adapters/ (Go to ADAPTER section)
            │
            ├─ Is this platform-specific (Bash/PowerShell/etc.)?
            │  └─ YES → CREATE in /examples/ (Go to EXAMPLE section)
            │
            └─ Is this truly a new, standalone concept?
               └─ YES → CREATE new document (rare; Go to CREATE NEW section)
               └─ NO → Re-examine classification; likely belongs in existing doc
```

---

## Integration Patterns

### Pattern 1: ENRICH (Update Existing Document)

**When:** Concept already documented; you have new information

**Approach:**

1. **Locate existing document**
   ```
   Search handbook by concept name
   Find docs/[category]/[name].md or docs/patterns/[name]/README.md
   ```

2. **Identify what to add**
   - [ ] Additional details about the concept?
   - [ ] New example?
   - [ ] New blocker or pitfall?
   - [ ] Better explanation?
   - [ ] Additional rainy path?

3. **Update appropriate section**
   ```markdown
   If adding details → Expand existing section
   If adding pitfall → Add to "Common Pitfalls" section
   If adding rainy path → Add to "Rainy Paths" section
   If adding example → Reference in core, create example file
   If adding adapter → Reference in core, create adapter file
   ```

4. **Update cross-links**
   - [ ] Link from processes that use this?
   - [ ] Link from related patterns?
   - [ ] Update README if applicable?

#### Example: Enriching Ralph Loop

**Situation:** New information about Claude-specific behavior

**Approach:**
- Core concept already exists: `docs/patterns/ralph-loop/README.md` ✓
- Claude adapter already exists: `docs/patterns/ralph-loop/adapters/claude.md` ✓
- New information is Claude-specific
- Action: **ENRICH** `docs/patterns/ralph-loop/adapters/claude.md`
- Add new section about the discovered behavior
- Do NOT create `docs/patterns/ralph-loop-claude.md`

---

### Pattern 2: ADD ADAPTER (Tool-Specific Variant)

**When:** You have guidance for Claude, Copilot, or OpenCode that differs from core

**Template:**

```markdown
# Ralph Loop: [Tool Name] Adapter

**Harness:** [Tool Name]  
**Prerequisite:** [Ralph Loop Pattern (Core Concept)](../README.md)

---

## Overview
[Brief explanation of how this tool differs or enhances the core]

## Configuration
[Tool-specific setup]

## Prompt Patterns
[Tool-specific prompt engineering]

## Limitations & Workarounds
[What doesn't work; how to work around]

## See Also
- [Core Ralph Loop Pattern](../README.md)
- [Related Adapters](../)
```

**Steps:**

1. Check if file exists: `docs/patterns/[name]/adapters/[tool].md`
   - If yes: ENRICH (update existing)
   - If no: CREATE

2. Write or update adapter document
   ```
   docs/patterns/ralph-loop/adapters/opencode.md
   docs/patterns/context-management/adapters/claude.md
   etc.
   ```

3. Update core concept README
   - Add reference to new adapter
   - Add to "Harness-Specific" section

4. Validate
   - Links work?
   - Adapter doesn't repeat core concept?
   - Clearly marked as tool-specific?

#### Example: Adding OpenCode Adapter

**Situation:** You've documented how Ralph Loop works with OpenCode

**File Structure:**
```
docs/patterns/ralph-loop/
├── README.md (core)
├── adapters/
│   ├── claude.md (existing)
│   ├── copilot.md (existing)
│   └── opencode.md (NEW)
```

**Update core README.md:**
```markdown
## Harness-Specific Implementations

See tool-specific guidance:
- [Claude Adapter](adapters/claude.md)
- [GitHub Copilot Adapter](adapters/copilot.md)
- [OpenCode Adapter](adapters/opencode.md) ← NEW
```

---

### Pattern 3: ADD EXAMPLE (Platform-Specific Implementation)

**When:** You have code/guidance for Bash, PowerShell, Python, JavaScript, etc.

**Template:**

```markdown
# [Pattern Name]: [Platform] Implementation

**Platform:** [Bash/PowerShell/Python/etc.]  
**Prerequisite:** [[Pattern Name] Pattern (Core Concept)](../README.md)

---

## Overview
[Brief explanation of how this platform implements the concept]

## Prerequisites
[What's needed to run this]

## The Implementation

### [Code section 1]
[Complete, working code]

### [Code section 2]
[Complete, working code]

## Usage

### Basic Usage
[How to run]

### Customization
[How to modify]

## Troubleshooting
[Common issues]

## See Also
- [Core Pattern](../README.md)
- [Other Platform Implementations](../)
```

**Steps:**

1. Check if file exists: `docs/patterns/[name]/examples/[platform].md`
   - If yes: ENRICH (update existing)
   - If no: CREATE

2. Write or update example document
   ```
   docs/patterns/ralph-loop/examples/bash.md
   docs/patterns/ralph-loop/examples/powershell.md
   docs/patterns/context-management/examples/python.md
   ```

3. Update core concept README
   - Add reference to new example
   - Add to "Platform Examples" or "Implementations" section

4. Validate
   - Code is complete and runnable?
   - Platform-specific, not universal?
   - Links work?

#### Example: Adding PowerShell Example

**Situation:** You've written Ralph Loop in PowerShell

**File Structure:**
```
docs/patterns/ralph-loop/
├── README.md (core)
├── examples/
│   ├── bash.md (existing)
│   └── powershell.md (NEW)
```

**Update core README.md:**
```markdown
## Platform Examples

- [Bash/Unix Implementation](examples/bash.md)
- [PowerShell/Windows Implementation](examples/powershell.md) ← NEW
```

---

### Pattern 4: CREATE NEW DOCUMENT (Rare)

**When:** This is a genuinely new concept not covered anywhere

**Warning:** This is rare. Most "new" things are variants of existing concepts.

**Pre-Flight Checklist:**

Before creating, verify:
- [ ] Searched handbook thoroughly for similar concepts?
- [ ] Could this be an example or adapter of something existing?
- [ ] Could this enrich an existing process or pattern?
- [ ] Is this foundational enough to document?
- [ ] Have I asked for guidance?

**Steps:**

1. **Choose category**
   - Principle? Pattern? Process? Playbook? Skill? Scenario?
   - (Reference, Adapter, Example don't create standalone)

2. **Choose location**
   ```
   Principle → docs/principles/[name].md
   Pattern → docs/patterns/[name]/README.md
   Process → docs/processes/[name].md
   Playbook → docs/playbooks/[name].md
   Skill → docs/skills/[name].md
   Scenario → docs/scenarios/[name].md
   ```

3. **Write document**
   - Use appropriate template (from README.md)
   - Include cross-links to related concepts
   - Document rainy paths if applicable

4. **Update navigation**
   - Add to README.md if it's important
   - Link from related documents
   - Add to appropriate "See Also" sections

5. **Validate**
   - All links work?
   - Related concepts cross-linked?
   - Navigation updated?

---

## Specific Integration Scenarios

### Scenario A: New Adapter (Tool-Specific Guidance)

**You have:** Claude-specific blocker detection patterns

**Classification:** Adapter (Claude)

**Search:** Does Ralph Loop adapter for Claude exist? → YES

**Action:** ENRICH `docs/patterns/ralph-loop/adapters/claude.md`
- Add new blocker detection section
- Update README to reference new section
- Validate links

---

### Scenario B: New Example (Platform-Specific Code)

**You have:** Ralph Loop implemented in Rust

**Classification:** Example (Rust)

**Search:** Does Ralph Loop example for Rust exist? → NO

**Action:** CREATE `docs/patterns/ralph-loop/examples/rust.md`
- Write complete Rust implementation
- Update core README to reference new example
- Link from related examples
- Validate

---

### Scenario C: Extending a Process

**You have:** Additional rainy path for software development process

**Classification:** Process

**Search:** Does software development process exist? → YES

**Action:** ENRICH `docs/processes/software-development.md`
- Add new rainy path section
- Explain blocker and recovery
- Link from relevant phase
- Validate

---

### Scenario D: Supporting a Pattern

**You have:** Step-by-step guide for applying tracer bullets

**Classification:** Playbook (specific how-to) or Pattern (technique)

**Search:** Does tracer bullets pattern exist? → YES

**Action:** Either:
- ENRICH `docs/patterns/tracer-bullets.md` with procedure
- OR CREATE `docs/playbooks/apply-tracer-bullets.md` for detailed how-to
- Link between pattern and playbook
- Validate

---

### Scenario E: New Pattern

**You have:** A repeated technique that doesn't fit existing patterns

**Classification:** Pattern

**Search:** Does this concept exist? → NO

**Action:** CREATE `docs/patterns/[name]/README.md`
- Document core concept
- Plan for examples and adapters
- Cross-link to processes that might use it
- Validate

---

## Integration Checklist

After completing integration:

- [ ] **Document Updated or Created**
  - Existing enriched, or new document created?
  - Content is clear and complete?
  - Code examples work?

- [ ] **Location Correct**
  - File in right folder structure?
  - Naming follows convention?
  - Subdirectories used appropriately?

- [ ] **Cross-Linking**
  - Core concept references examples/adapters?
  - Examples/adapters link back to core?
  - Related concepts link to each other?
  - Process/pattern references updated?

- [ ] **Separation of Concerns**
  - Core concept is tool/platform agnostic?
  - Tool-specific content in /adapters/?
  - Platform-specific content in /examples/?
  - No unnecessary duplication?

- [ ] **Navigation**
  - README.md updated if important?
  - Quick Navigation updated if applicable?
  - "See Also" sections current?

- [ ] **Style & Format**
  - Follows handbook conventions?
  - Headings are clear?
  - Code properly formatted?
  - Links are relative and work?

- [ ] **Completeness**
  - No TODO items left behind?
  - No broken links?
  - All references valid?

---

## Common Integration Mistakes

### Mistake 1: Creating Tool-Specific File at Root

❌ `docs/patterns/ralph-loop-claude.md`  
✅ `docs/patterns/ralph-loop/adapters/claude.md`

**Why:** Tool-specific guidance belongs in `/adapters/` so it's clearly optional.

### Mistake 2: Duplicating Concept

❌ Writing "Ralph Loop" explanation twice:
- In `docs/patterns/ralph-loop/README.md`
- In `docs/patterns/ralph-loop/adapters/claude.md`

✅ Core concept in README, adapter explains only Claude-specific parts

**Why:** Duplication causes maintenance burden; changes must be made in multiple places.

### Mistake 3: Not Updating Cross-Links

❌ Create new adapter but forget to link from core concept

✅ Update core concept README to reference new adapter

**Why:** Without links, readers won't discover the new content.

### Mistake 4: Hiding New Content

❌ Create `docs/patterns/context-management/examples/powershell.md` but don't mention it in README

✅ Add reference to core concept: "See [PowerShell Implementation](examples/powershell.md)"

**Why:** Without navigation, content might as well not exist.

### Mistake 5: Mixing Concerns

❌ Core concept explains "use Claude API with this endpoint"

✅ Core explains the technique; adapter explains Claude-specific API details

**Why:** Core concept should work with any tool.

---

## Integration Examples: Complete Walkthrough

### Complete Example: Adding OpenCode Adapter

**Step 1: Classify**
```
Input: Documentation on how Ralph Loop works with OpenCode
Classification: Pattern (Ralph Loop) + Adapter (OpenCode)
```

**Step 2: Search**
```
Search for: Ralph Loop → FOUND
Search for: Ralph Loop + OpenCode adapter → NOT FOUND
Decision: Need to create adapter; core concept already exists
```

**Step 3: Create Adapter**
```
File: docs/patterns/ralph-loop/adapters/opencode.md

Content sections:
- Overview (how OpenCode differs)
- Configuration (OpenCode setup)
- Key features (what makes it different)
- Limitations (what doesn't work)
- See Also (links)
```

**Step 4: Update Core**
```
File: docs/patterns/ralph-loop/README.md

Add to "Harness-Specific Implementations" section:
- [OpenCode Adapter](adapters/opencode.md) ← NEW
```

**Step 5: Cross-Link**
```
In adapter: Link back to core
  Prerequisite: [Ralph Loop Pattern](../README.md)

In core: Link to adapter
  See adapter guidance: [OpenCode](adapters/opencode.md)

In process Phase 4.2 (AFK Implementation): Already links to Ralph Loop ✓
```

**Step 6: Validate**
```
- [ ] Adapter file created? YES
- [ ] Core concept updated? YES
- [ ] Links work? Check...
- [ ] No duplication? Adapter only covers OpenCode-specific
- [ ] Navigation updated? YES
- [ ] Style matches? YES
```

**Summary:**
- Created 1 new file: `docs/patterns/ralph-loop/adapters/opencode.md`
- Updated 1 existing file: `docs/patterns/ralph-loop/README.md` (added link)
- No duplication
- All cross-links working

---

## See Also

- **[README.md](README.md)** — Authoring guide overview
- **[classify.md](classify.md)** — Classification (before integration)
- **[validate.md](validate.md)** — Validation (after integration)
- **[../../AGENTS.md](../../AGENTS.md)** — Quick AI agent reference

