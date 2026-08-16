# Validation Guide

**Purpose:** How to validate handbook changes for quality and coherence  
**Audience:** AI agents after integration; humans reviewing changes  
**Prerequisite:** [integrate.md](integrate.md) — Integration complete  
**Related:** [README.md](README.md) (overview)

---

## Validation Phases

Validation happens in four phases:

1. **Content Validation** — Is the content correct and complete?
2. **Structure Validation** — Is the hierarchy and organization sound?
3. **Navigation Validation** — Can readers find what they need?
4. **Quality Validation** — Does it meet handbook standards?

---

## Phase 1: Content Validation

**Goal:** Verify content is accurate, complete, and appropriate

### Content Checklist

- [ ] **Accuracy**
  - [ ] Claims are factually correct?
  - [ ] Examples are accurate?
  - [ ] Code examples are syntactically correct?
  - [ ] No contradictions with existing documentation?

- [ ] **Completeness**
  - [ ] Concept fully explained?
  - [ ] No "TODO" items left?
  - [ ] Examples are complete (can run them)?
  - [ ] Edge cases addressed?

- [ ] **Appropriateness**
  - [ ] Belongs in this category (P/P/Pr/Pl/S/Sc/A/E/R)?
  - [ ] Content level is right for audience?
  - [ ] Technical depth is appropriate?
  - [ ] Language is clear and accessible?

- [ ] **Separation of Concerns**
  - [ ] Core concept has no tool/platform assumptions?
  - [ ] Tool-specific guidance in /adapters/ folder?
  - [ ] Platform-specific code in /examples/ folder?
  - [ ] No unnecessary duplication?

### Content Validation Example

**Document:** `docs/patterns/ralph-loop/adapters/opencode.md`

**Checks:**

```
Accuracy:
✓ OpenCode configuration is current
✓ API endpoints are correct
✓ Feature descriptions match OpenCode docs
✓ No contradictions with Claude or Copilot adapters

Completeness:
✓ All configuration options explained
✓ No TODO markers
✓ Code examples are complete
✓ Error handling covered

Appropriateness:
✓ Content is OpenCode-specific (adapter ✓)
✓ Level matches other adapters
✓ Technical depth appropriate
✓ Clear for OpenCode users

Separation:
✓ Core concept (README) has no OpenCode refs
✓ This file only has OpenCode-specific content
✓ No duplication with Claude/Copilot adapters
```

---

## Phase 2: Structure Validation

**Goal:** Verify hierarchy and organization are sound

### Structure Checklist

- [ ] **Document Structure**
  - [ ] Uses appropriate template?
  - [ ] Headings are clear and hierarchical?
  - [ ] Sections flow logically?
  - [ ] Lists properly formatted?

- [ ] **File Hierarchy**
  - [ ] File in correct location?
  - [ ] Folder structure is logical?
  - [ ] Naming convention followed?
  - [ ] Subdirectories used appropriately?

- [ ] **Consistency**
  - [ ] Matches style of similar documents?
  - [ ] Same terminology as elsewhere?
  - [ ] Same code formatting conventions?
  - [ ] Same markdown formatting?

### Structure Validation Example

**Document:** New playbook `docs/playbooks/apply-vertical-slicing.md`

**Checks:**

```
Document Structure:
✓ Follows playbook template
✓ Clear headings: When to Use, Steps, Verification, Mistakes
✓ Logical flow: overview → procedure → validation
✓ Lists properly numbered

File Hierarchy:
✓ Located in docs/playbooks/ (correct)
✓ Filename is descriptive: apply-vertical-slicing.md
✓ Not nested under patterns/ (would duplicate)
✓ Not nested under processes/ (separate concept)

Consistency:
✓ Matches style of other playbooks
✓ Uses same terminology as vertical-slicing pattern
✓ Code examples in same style as others
✓ Cross-link format matches handbook
```

---

## Phase 3: Navigation Validation

**Goal:** Verify readers can find and understand connections between content

### Navigation Checklist

- [ ] **Discovery**
  - [ ] Content linked from README if important?
  - [ ] Discoverable via quick navigation?
  - [ ] Appears in relevant "See Also" sections?
  - [ ] Hierarchical links clear?

- [ ] **Cross-Linking**
  - [ ] All links point to existing documents?
  - [ ] No broken links?
  - [ ] Links are relative, not absolute?
  - [ ] Backward links exist (A links to B, B links to A)?

- [ ] **Context**
  - [ ] Reader knows what category this is?
  - [ ] Reader understands relationship to other concepts?
  - [ ] Related concepts are clearly marked?
  - [ ] Prerequisites are documented?

- [ ] **Entry Points**
  - [ ] Accessible from relevant process?
  - [ ] Accessible from related patterns?
  - [ ] Scenario links to it if applicable?
  - [ ] Multiple paths to same content?

### Navigation Validation Example

**Document:** Added `docs/patterns/ralph-loop/adapters/opencode.md`

**Checks:**

```
Discovery:
✓ Core README.md updated with link
✓ Quick navigation didn't need update (doesn't list adapters)
✓ Added to "Harness-Specific Implementations" section
✓ Clear relationship to core pattern

Cross-Linking:
✓ All links point to existing files
✓ Link to core: ../README.md (valid relative path)
✓ Link from core: adapters/opencode.md (valid)
✓ Bidirectional linking: core ↔ adapter

Context:
✓ Marked as "Adapter" in heading
✓ Prerequisite: [Ralph Loop Pattern] clearly stated
✓ Related adapters linked
✓ Purpose is clear

Entry Points:
✓ Accessible from process Phase 4.2 → Ralph Loop → adapters
✓ Accessible from core pattern README → adapters section
✓ Multiple paths to reach it
✓ Natural discovery path for OpenCode users
```

---

## Phase 4: Quality Validation

**Goal:** Verify overall quality meets handbook standards

### Quality Checklist

- [ ] **Writing Quality**
  - [ ] Clear, concise language?
  - [ ] No jargon without explanation?
  - [ ] Active voice where appropriate?
  - [ ] Professional tone?

- [ ] **Technical Quality**
  - [ ] Code examples are correct?
  - [ ] Configuration is current?
  - [ ] No deprecated practices?
  - [ ] Security best practices followed?

- [ ] **Completeness for Category**
  - [ ] Pattern: Decision points, flow, examples, adapters?
  - [ ] Playbook: Steps, verification, common mistakes?
  - [ ] Process: Phases, rainy paths, cross-links?
  - [ ] Adapter: Configuration, limitations, tool-specific behavior?
  - [ ] Example: Working code, setup, usage?

- [ ] **Maintenance**
  - [ ] No TODO items?
  - [ ] No comments indicating incomplete work?
  - [ ] Maintainable by future agents/humans?
  - [ ] Clear intent and reasoning?

### Quality Validation Example

**Document:** New pattern `docs/patterns/context-focus/README.md`

**Checks:**

```
Writing Quality:
✓ Clear explanation of concept
✓ Technical terms explained
✓ Examples are concrete
✓ Professional, consistent tone

Technical Quality:
✓ Advice is current and sound
✓ Code examples follow best practices
✓ Security implications addressed
✓ No deprecated patterns

Completeness for Pattern:
✓ Core concept explained (no tool assumptions)
✓ Decision points identified (3)
✓ Decision flow documented (visual diagram)
✓ Common pitfalls included (4 scenarios)
✓ Related patterns linked
✓ Adapters noted (Claude, Copilot, OpenCode)
✓ Examples noted (Bash, PowerShell)

Maintenance:
✓ No TODO markers
✓ Clear reasoning documented
✓ Rationale for design decisions clear
✓ Future maintainers can understand it
```

---

## Validation Procedures

### Procedure 1: Automated Link Checking

**Goal:** Verify all links point to valid files

**Steps:**

1. Extract all links from document
   ```
   Pattern: [text](link)
   Extract: link
   ```

2. For each link:
   - [ ] Is it relative? (e.g., `../README.md`, not `/docs/README.md`)
   - [ ] Does file exist at that path?
   - [ ] Is it the right file? (content matches link text)

3. Check cross-links:
   - [ ] If A links to B, does B link back to A?
   - [ ] Bidirectional references complete?

**Tools:**
- Manual: Use file explorer to verify paths
- Script: Regular expressions to extract and check links
- IDE: Hover over links to see target file

### Procedure 2: Content Review

**Goal:** Verify content quality and accuracy

**Steps:**

1. Read document completely
2. For each section, ask:
   - [ ] Is this clear?
   - [ ] Is this accurate?
   - [ ] Is this complete?
   - [ ] Does this fit the category?
3. Check against handbook standards
4. Verify examples work (if applicable)

### Procedure 3: Structure Review

**Goal:** Verify hierarchy and organization

**Steps:**

1. Look at file location
   - [ ] Is it in the right folder?
   - [ ] Is naming appropriate?
2. Look at document outline
   - [ ] Do sections flow logically?
   - [ ] Are headings clear?
   - [ ] Is structure consistent with similar docs?
3. Check for duplication
   - [ ] Is this concept documented elsewhere?
   - [ ] If yes, why is separate doc necessary?

### Procedure 4: Navigation Review

**Goal:** Verify readers can find content

**Steps:**

1. From README.md, can you find this doc?
   - [ ] Via quick navigation?
   - [ ] Via process links?
   - [ ] Via pattern links?
   - [ ] Via scenario?
2. From related docs, can you find this?
   - [ ] Is it in "See Also" sections?
   - [ ] Is it in "Related" lists?
3. Are cross-links bidirectional?
   - [ ] Does this doc link to others?
   - [ ] Do those others link back?

---

## Validation Checklist (Comprehensive)

### Before Marking Complete

#### Content
- [ ] Factually accurate
- [ ] Technically correct
- [ ] Complete (no TODOs)
- [ ] Appropriate depth for audience
- [ ] Clear and well-written
- [ ] No unnecessary duplication
- [ ] Tool/platform separation maintained

#### Structure
- [ ] Uses appropriate template
- [ ] Clear heading hierarchy
- [ ] Logical section flow
- [ ] Consistent with similar documents
- [ ] Follows naming conventions
- [ ] In correct file location

#### Navigation
- [ ] All links work (no broken links)
- [ ] Links are relative paths
- [ ] Cross-links bidirectional
- [ ] Discoverable from README or navigation
- [ ] Related concepts linked
- [ ] Prerequisites documented

#### Quality
- [ ] Professional tone
- [ ] Active voice where appropriate
- [ ] No undefined jargon
- [ ] Code examples work
- [ ] Examples are complete
- [ ] Security best practices
- [ ] No deprecated practices
- [ ] Future-maintainable

#### Completeness for Type
- [ ] All required sections present
- [ ] References included if needed
- [ ] Examples included if needed
- [ ] Adapters referenced if needed
- [ ] Integration points documented

---

## Common Validation Issues

### Issue 1: Broken Links

**Problem:** Links point to non-existent files

**Detection:**
```
Linked: ../patterns/unknown-pattern.md
File exists? NO → Broken link
```

**Fix:** Either:
- Correct the path
- Create the target document
- Remove the link

**Prevention:** Verify file exists before creating link

### Issue 2: Duplication

**Problem:** Same concept documented multiple places

**Detection:**
```
Ralph Loop explained in:
- docs/patterns/ralph-loop/README.md ✓
- docs/patterns/ralph-loop/adapters/claude.md (should not repeat!)
- docs/processes/software-development.md (should reference, not repeat)
```

**Fix:**
- Keep in one authoritative location (usually core concept)
- Other places reference it
- Adapters explain tool-specific behavior only

**Prevention:** Search before writing; enrich existing docs

### Issue 3: Tool Assumption in Core

**Problem:** Core concept assumes Claude, Bash, etc.

**Detection:**
```
Pattern README says: "Use the Claude API..."
Should not! This is adapter guidance.
```

**Fix:**
- Remove tool-specific guidance
- Move to appropriate adapter
- Update core to reference adapter

**Prevention:** Review core concept to ensure tool-agnostic

### Issue 4: Missing Cross-Links

**Problem:** Reader doesn't know about related concepts

**Detection:**
```
"Tracer Bullets" pattern doesn't mention "Vertical Slicing"
Both are related but not linked
Reader doesn't know the relationship
```

**Fix:**
- Add "Related Patterns" section
- Link bidirectionally
- Explain the relationship

**Prevention:** During integration, check for related concepts

### Issue 5: Incomplete Documentation

**Problem:** Document has TODO items or incomplete sections

**Detection:**
```
[TODO: Add PowerShell example]
Incomplete examples section
```

**Fix:** Complete the documentation before releasing

**Prevention:** Review entire document for completeness

### Issue 6: Inconsistent Style

**Problem:** Different documentation style than similar documents

**Detection:**
```
Other playbooks use: "## Step 1:"
This playbook uses: "1. Step one:"
Inconsistency
```

**Fix:** Align with handbook style conventions

**Prevention:** Follow template when creating documents

---

## Validation Workflow

```
Document Created/Updated
        │
        ▼
CONTENT VALIDATION
├─ Accurate?
├─ Complete?
├─ Appropriate?
└─ Correct separation of concerns?
        │
        ▼
STRUCTURE VALIDATION
├─ Correct location?
├─ Uses template?
├─ Consistent style?
└─ Logical hierarchy?
        │
        ▼
NAVIGATION VALIDATION
├─ All links work?
├─ Cross-links bidirectional?
├─ Discoverable from README?
└─ Related concepts linked?
        │
        ▼
QUALITY VALIDATION
├─ Professional quality?
├─ Technical accuracy?
├─ Complete for category?
└─ Maintainable?
        │
        ▼
ISSUES FOUND?
├─ YES → Fix issues → Re-validate
└─ NO → COMPLETE ✓
```

---

## Validation Report Template

When reporting validation results:

```markdown
## Validation Report: [Document Name]

### Content Validation
- ✓ Factually accurate
- ✓ Complete (no TODOs)
- ✓ Appropriate depth
- ✗ One claim contradicts [existing doc] → [Action]
- Status: PASS / NEEDS REVISION

### Structure Validation
- ✓ Correct location
- ✓ Uses template
- ✓ Consistent style
- Status: PASS / NEEDS REVISION

### Navigation Validation
- ✓ All links valid
- ✓ Bidirectional cross-links
- ✓ Discoverable from README
- ✗ Missing link from [related doc] → [Action]
- Status: PASS / NEEDS REVISION

### Quality Validation
- ✓ Professional quality
- ✓ Technical accuracy
- ✓ Complete examples
- Status: PASS / NEEDS REVISION

### Overall Status
[PASS / NEEDS REVISION / BLOCKED]

### Action Items (if any)
1. [Issue] → [Suggested fix]
2. [Issue] → [Suggested fix]
```

---

## See Also

- **[README.md](README.md)** — Authoring guide overview
- **[integrate.md](integrate.md)** — Integration (before validation)
- **[classify.md](classify.md)** — Classification (before integration)
- **[../../AGENTS.md](../../AGENTS.md)** — Quick AI agent reference

