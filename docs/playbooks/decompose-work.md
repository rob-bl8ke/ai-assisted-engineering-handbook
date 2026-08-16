# Playbook: Decompose Work Into Issues

## Overview

This playbook provides a systematic approach to breaking down a PRD or feature requirement into a set of implementable, reviewable, and testable GitHub issues. Each issue represents a vertical slice (see [Vertical Slicing Pattern](../patterns/vertical-slicing.md)) that can be implemented, reviewed, and deployed independently.

## When to Use This Playbook

- You have a PRD and need to create implementation work items
- You need to plan a feature's development as a series of pull requests
- You're creating work for a team (human or AI agents) to execute
- You want to decompose work following [Vertical Slicing](../patterns/vertical-slicing.md) principles
- You're in Phase 3 (Decomposition) of the [Software Development Process](../processes/software-development.md#phase-3-decomposition)

## Key Principle: Vertical Slicing

Each issue should represent a thin, end-to-end slice:
- Touches all relevant layers (not just frontend, not just database)
- Independently testable
- Independently reviewable and deployable
- Delivers measurable value
- Small enough to implement in 1-3 days

See [Vertical Slicing Pattern](../patterns/vertical-slicing.md) for detailed guidance.

---

## Phase 1: Understand the PRD (15-20 minutes)

### 1.1 Identify Core Features

**Objective:** Break the PRD into distinct, independently valuable capabilities.

**Steps:**
1. Read the PRD (see [Create PRD Playbook](create-prd.md))
2. Identify major functional requirements (FR-1, FR-2, etc.)
3. Group related requirements
4. Rank by priority and dependencies
5. Note which features can be built in parallel

**What to capture:**
```
## Core Features Identified

### Feature 1: [Name]
- Requirements: [FR-1, FR-3]
- Dependencies: [Feature 2 must exist first]
- Priority: [Must-have / Nice-to-have]
- Estimated complexity: [Small / Medium / Large]

### Feature 2: [Name]
- Requirements: [FR-2]
- Dependencies: [None]
- Priority: [Must-have]
- Estimated complexity: [Medium]
```

**Example:**

```
## Core Features Identified

### Feature 1: Mark Decision Points
- Requirements: FR-1 (Define decision point markers)
- Dependencies: None
- Priority: Must-have (blocks other features)
- Estimated complexity: Small

### Feature 2: Agent Clarification Request
- Requirements: FR-2 (Agent responds with clarification request)
- Dependencies: Feature 1 (must have decision points first)
- Priority: Must-have
- Estimated complexity: Medium

### Feature 3: Developer Response Collection
- Requirements: FR-3 (Developer provides decision)
- Dependencies: Feature 2
- Priority: Must-have
- Estimated complexity: Medium

### Feature 4: Decision History
- Requirements: FR-4 (nice-to-have)
- Dependencies: Features 1-3
- Priority: Nice-to-have (defer to Phase 2)
- Estimated complexity: Small
```

### 1.2 Plan Dependencies and Sequencing

**Objective:** Determine order: what must be built first?

**Steps:**
1. For each feature, list its dependencies
2. Identify features with no dependencies (start here)
3. Identify the critical path (longest dependency chain)
4. Identify what can be built in parallel
5. Plan a sequence that allows parallel work

**What to capture:**
```
## Sequencing Plan

### Phase 1 (Foundation - No Dependencies)
- [Feature that has no dependencies]
- [Another feature with no dependencies]

### Phase 2 (Building on Phase 1)
- [Feature that depends on Phase 1]
- [Feature that depends on Phase 1]

### Phase 3 (Building on Phases 1-2)
- [Feature that depends on earlier phases]

### Parallelizable Work
- [Features that can be done in parallel]
```

**Example:**

```
## Sequencing Plan

### Phase 1 (Foundation - No Dependencies)
- Mark Decision Points in code (Feature 1)

### Phase 2 (Parallel Tracks)
- Agent Clarification Request (Feature 2) - requires Feature 1
- Developer Response Collection (Feature 3) - requires Feature 1

### Phase 3 (Polish)
- Decision History (Feature 4) - requires Features 1-3

### Parallelizable Work
- Features 2 and 3 can be built in parallel (both depend only on Feature 1)
```

---

## Phase 2: Define Vertical Slices (30-45 minutes)

### 2.1 Identify the Thinnest Slice

**Objective:** Find the smallest, end-to-end slice that demonstrates the core workflow.

**Steps:**
1. Choose one feature to slice (usually start with highest-priority)
2. Ask: What's the minimal implementation that proves this works end-to-end?
3. Identify which components/layers must be touched:
   - Frontend (UI, interaction)
   - Backend (API, business logic)
   - Database (data storage)
   - External integrations
4. For each layer, include the minimum needed to support the end-to-end flow
5. Exclude: error handling, edge cases, optimization, polish

**What to capture:**
```
## Slice 1: [Slice Name]

**Purpose:** [What this slice delivers]

**End-to-End Flow:**
[Brief description of user interaction]

**Components Touched:**
- Frontend: [Minimal UI needed]
- Backend: [Minimal API needed]
- Database: [Minimal schema needed]
- External: [Any integrations needed]

**Deliverable:** [User can do X in the system]

**What's NOT in this slice:**
- [Error case that's deferred]
- [Edge case that's deferred]
- [Optimization that's deferred]

**Acceptance Criteria:**
- [ ] User can perform the end-to-end flow
- [ ] All code has tests
- [ ] Code follows project patterns
- [ ] Code reviewed and approved
```

**Example:**

```
## Slice 1: Mark Decision Points

**Purpose:** Developers can annotate decision points in requirements; system captures them.

**End-to-End Flow:**
1. Developer opens requirements file
2. Developer marks a line with decision point marker (e.g., @decision)
3. Developer saves file
4. System parses file and logs the decision point
5. Developer runs test to verify decision point was captured

**Components Touched:**
- Marker parser (new module): Detect @decision markers in text
- Decision checkpoint model: Store marker info
- Tests: Verify parser works

**Deliverable:** System can parse decision markers and create checkpoints

**What's NOT in this slice:**
- Agent integration (comes later)
- UI for managing markers (comes later)
- Persistence beyond current process run
- Error handling for malformed markers

**Acceptance Criteria:**
- [ ] Parser extracts decision markers from text
- [ ] Parser creates DecisionCheckpoint objects
- [ ] All parser logic has unit tests
- [ ] Example requirements file with markers provided
- [ ] Code reviewed and approved
```

### 2.2 Create Additional Slices

**Objective:** Define remaining slices that layer on top of the first.

**Steps:**
1. For each slice after the first, identify what it builds on
2. Each slice should:
   - Add one meaningful capability
   - Include all affected layers (frontend, backend, etc.)
   - Be independently testable
   - Complement previous slices without requiring replacement
3. Order slices: simple → complex, dependencies first
4. Ensure each slice is ~1-3 days of work

**What to capture:**
```
## Slice 2: [Name]
**Builds on:** [Slice 1]
**Purpose:** [What this adds]
**Acceptance Criteria:**
- [ ]
- [ ]

## Slice 3: [Name]
**Builds on:** [Slices 1-2]
**Purpose:** [What this adds]
**Acceptance Criteria:**
- [ ]
- [ ]
```

**Example:**

```
## Slice 2: Agent Pause and Clarification Request

**Builds on:** Slice 1 (Decision points are parsed)
**Purpose:** When agent encounters a decision point, it pauses and asks the developer for input

**End-to-End Flow:**
1. Agent starts implementing with AFK flag
2. Agent encounters decision point during work
3. Agent formats clarification request (question + options)
4. Agent sends request to developer queue
5. Developer receives notification with decision request
6. Agent waits for response (with timeout)

**Components Touched:**
- Agent controller: Add pause-and-ask capability
- Decision request formatter: Take checkpoint and produce question
- Notification service: Send decision request to developer
- Test: Verify agent pauses correctly, formats request correctly

**Deliverable:** Agent can encounter a decision point and ask developer what to do

**What's NOT in this slice:**
- Developer response collection (next slice)
- Agent resumption (comes after developer responds)
- Persistence of decision request beyond session
- UI improvements (basic notification only)

**Acceptance Criteria:**
- [ ] Agent can pause at decision checkpoints
- [ ] Decision request is formatted with question + options
- [ ] Notification is sent to developer
- [ ] Agent times out if response not received (default: 30 min)
- [ ] All logic has integration tests
- [ ] Code reviewed and approved
```

---

## Phase 3: Create Implementation Issues (30-45 minutes)

### 3.1 Convert Each Slice to a GitHub Issue

**Objective:** Translate each slice into a concrete, implementable GitHub issue.

**Steps:**
1. For each slice, create a GitHub issue with:
   - Title: Clear, actionable (verb + noun)
   - Description: Problem, approach, acceptance criteria
   - Labels: feature, ready-for-agent (or ready-for-human)
   - Depends on: Link to earlier slices (if any)
   - Size estimate: Small (1-2 days), Medium (2-3 days), Large (4+ days)
2. Make issues specific enough that an AI agent can implement from them
3. Link issues to the PRD
4. Set up dependency relationships on GitHub

**Issue Template:**

```markdown
# [Verb] [What] - [Slice Name]

## Related PRD
Link to: [Feature PRD](../docs/prd-file.md)

## What to Build

[Clear description of what should be built]

End-to-end flow:
- User does X
- System does Y
- Result is Z

## Approach

[How to implement this; which patterns to follow]

- Apply [Pattern 1](../patterns/pattern-1.md)
- Apply [Pattern 2](../patterns/pattern-2.md)
- [Specific implementation guidance]

## Acceptance Criteria

- [ ] End-to-end flow works as described
- [ ] Code follows [project patterns]
- [ ] All new code has tests ([test strategy])
- [ ] Tests pass locally
- [ ] PR reviewed and approved

## Definition of Done

- [ ] Issue closed when PR merged
- [ ] All acceptance criteria verified
- [ ] No test regressions

## Blocked By (if any)
- Issue #X (describe why)

## Depends On (if any)
- Slice 1: Issue #A
- Slice 2: Issue #B
```

**Example Issues:**

```markdown
# Issue #101: Add Decision Point Marker Parser

## Related PRD
[Feature PRD - Agent Clarification Requests](../docs/prd/agent-clarification.md)

## What to Build

Create a parser that detects decision point markers (@decision) in text and creates
DecisionCheckpoint objects that the system can use later.

End-to-end flow:
- Developer writes requirements with @decision markers
- System reads requirements file
- Parser extracts markers and creates checkpoints
- Checkpoints are available to the agent

## Approach

- Create new `DecisionMarkerParser` class
- Apply [TDD Pattern](../patterns/test-driven-development.md): write tests first
- Apply [Context Management Pattern](../patterns/context-management.md): markers should be unambiguous
- Store parsed markers in memory during test

## Acceptance Criteria

- [ ] Parser recognizes @decision markers in text
- [ ] Parser extracts marker position, context, and options
- [ ] DecisionCheckpoint objects created correctly
- [ ] Invalid markers handled gracefully (error message logged)
- [ ] All parsing logic has unit tests (100% coverage)
- [ ] Example requirements file with markers provided as test data

## Definition of Done

- [ ] PR merged to main
- [ ] All tests pass in CI/CD
- [ ] No performance regressions

---

# Issue #102: Agent Pause at Decision Point

## Related PRD
[Feature PRD - Agent Clarification Requests](../docs/prd/agent-clarification.md)

## Blocked By
- Issue #101 (Decision points must be parsed first)

## What to Build

When an agent encounters a decision point during implementation, it should pause,
ask the developer for clarification, and wait for a response.

End-to-end flow:
- Agent starts with AFK flag
- Agent works through implementation
- Agent encounters a parsed decision point
- Agent pauses and formats a clarification question
- Agent sends question to developer
- Agent waits for response (or timeout)

## Approach

- Update Agent class to check for decision points
- Create `DecisionRequest` formatter
- Apply [TDD Pattern](../patterns/test-driven-development.md): test agent pause behavior
- Apply [Context Management Pattern](../patterns/context-management.md): keep question focused
- Use [Tracer Bullets Pattern](../patterns/tracer-bullets.md): build minimal implementation first

## Acceptance Criteria

- [ ] Agent pauses when encountering decision point
- [ ] Question is formatted with: problem, options, context
- [ ] Developer is notified of pending decision
- [ ] Agent waits up to 30 minutes for response
- [ ] After timeout, agent logs warning and continues with default
- [ ] All agent pause logic has integration tests

## Definition of Done

- [ ] PR merged to main
- [ ] Integration tests pass
- [ ] No performance regressions

---

# Issue #103: Collect Developer Decision Response

## Related PRD
[Feature PRD - Agent Clarification Requests](../docs/prd/agent-clarification.md)

## Blocked By
- Issue #102 (Agent must pause and ask first)

## What to Build

Developer can provide a decision response to an agent's clarification request.
Response is captured, validated, and made available to agent for resumption.

## Approach

- Create `DecisionResponse` handler
- Apply [TDD Pattern](../patterns/test-driven-development.md)
- Responses collected via API or CLI (decision based on user feedback)
- Invalid responses rejected with clear error message

## Acceptance Criteria

- [ ] Developer can submit decision response
- [ ] Response is validated against decision options
- [ ] Invalid responses rejected with error message
- [ ] Response is persisted (for audit trail)
- [ ] Agent can retrieve response when resuming
- [ ] All response handling has unit and integration tests

## Definition of Done

- [ ] PR merged to main
- [ ] All tests pass
- [ ] No regressions
```

---

## Phase 4: Link and Organize Work (15-20 minutes)

### 4.1 Set Up GitHub Issue Dependencies

**Objective:** Make issue dependencies explicit so work can be scheduled correctly.

**Steps:**
1. Open each issue in GitHub
2. For issues with dependencies, add "Blocked by" comment/field:
   - Issue #102 is blocked by Issue #101
   - Issue #103 is blocked by Issue #102
3. Use GitHub milestones to group related issues
4. Use GitHub projects or labels for sprint planning

**Example structure:**
```
Sprint 1:
├─ Issue #101 (Mark Decision Points) - no dependencies
└─ (Parallel) Issue #102 (Agent Pause) - depends on #101

Sprint 2:
├─ Issue #103 (Collect Response) - depends on #102
└─ (Parallel) Issue #104 (Decision History) - depends on #101-#103
```

### 4.2 Document Implementation Order

**Objective:** Create a clear roadmap for implementation.

**Steps:**
1. Create a `DECOMPOSITION.md` file documenting:
   - Overall feature being built
   - Slicing strategy (why these slices)
   - Slices in order
   - Which slices can be parallel
   - Which slices are optional (nice-to-have)
2. Link this file from the PRD
3. Use this to guide team on priority and sequencing

**Example DECOMPOSITION.md:**

```markdown
# Decomposition: Agent Clarification Requests

## Feature Being Built
Agents can pause during AFK implementation to ask developers for clarification on ambiguous decisions.

## Slicing Strategy
Vertical slices, each delivering end-to-end value:
1. **Slice 1:** Ability to mark and parse decision points
2. **Slice 2:** Agent recognizes decision points and pauses
3. **Slice 3:** Developer can respond to agent's question
4. **Slice 4 (Optional):** View decision history

Each slice can be tested independently and deployed separately.

## Implementation Order

### Phase 1 (Required Foundation)
- Issue #101: Parse Decision Point Markers → Branch: `feat/decision-markers`
- Estimated: 1-2 days
- No dependencies

### Phase 2 (Core Workflow - Parallel Tracks)
- Issue #102: Agent Pause at Decision Point → Branch: `feat/agent-pause`
  - Depends on: #101
  - Estimated: 2-3 days
  
- Issue #103: Collect Developer Response → Branch: `feat/collect-response`
  - Depends on: #102
  - Estimated: 2-3 days

### Phase 3 (Polish - Optional)
- Issue #104: Decision History Tracking → Branch: `feat/decision-history`
  - Depends on: #101-#103
  - Estimated: 1-2 days
  - **Nice-to-have:** Can defer to next release if time is short

## Parallelizable Work
After #101 is merged: #102 and #103 can be started simultaneously by different developers/agents.

## Critical Path
Longest dependency chain: #101 → #102 → #103 (5-7 days minimum)

## Success Criteria for Entire Decomposition
- [ ] All required issues (#101-#103) merged and deployed
- [ ] Agent successfully pauses for clarification on real implementation
- [ ] Developer successfully provides decisions
- [ ] No test regressions
- [ ] Performance impact < 5% on agent execution time
```

---

## Phase 5: Review and Refine (15-20 minutes)

### 5.1 Decomposition Checklist

**Before you consider this decomposition complete:**

```
[ ] Each issue is independently implementable
[ ] Each issue touches all relevant layers (full-stack slice)
[ ] Each issue has clear acceptance criteria
[ ] Each issue is sized: small (1-2 days), medium (2-3 days), or large (4+ days)
[ ] Dependencies are documented and correct
[ ] Issues link back to the PRD
[ ] Issues link to relevant patterns
[ ] Nice-to-have features are clearly marked
[ ] Work can proceed in parallel where possible
[ ] Total estimated work matches PRD scope
[ ] At least one other person reviewed the decomposition
```

### 5.2 Common Decomposition Mistakes

**Avoid these patterns:**

| Mistake | What Happens | How to Fix |
|---------|--------------|-----------|
| **Horizontal Slices** | Issues split by layer (all backend, then all frontend) | Reorganize so each issue touches all layers end-to-end |
| **Too Large** | One issue is 2+ weeks of work | Split issue further; identify intermediate milestones |
| **Missing Dependencies** | Team starts work on wrong thing first | Map dependencies explicitly; order work correctly |
| **Unclear Acceptance** | Team completes work but it's not what you wanted | Make acceptance criteria specific and testable |
| **Over-Specified** | Each issue specifies exactly how to implement | Specify WHAT, let implementer decide HOW |
| **Missing Integration** | Issues work independently but don't fit together | Add integration slice or test that verifies slices work together |

---

## Phase 6: Create Epic or Tracking Issue (10 minutes)

**Objective:** Create a parent issue that tracks all slices.

**Steps:**
1. Create a parent issue titled: "Epic: [Feature Name]" or "Tracking Issue: [Feature Name]"
2. Include summary of all slices
3. Link all child issues
4. Add a progress checklist
5. Use this as the primary reference for status

**Example Epic:**

```markdown
# Epic: Agent Clarification Requests

## Overview
Agents can pause during AFK implementation to ask developers for clarification when encountering ambiguous decisions.

## Why This Matters
Currently, agents get stuck when encountering ambiguous requirements. They can't proceed without human input, and developers must interrupt work to help. This feature allows agent to pause gracefully, ask a focused question, and resume without context loss.

## Implementation Slices

### Slice 1: Decision Point Markers
- Issue: #101 - Parse decision markers
- Status: [ ] Not Started
- Estimated: 1-2 days

### Slice 2: Agent Pause and Ask
- Issue: #102 - Agent pauses at decision point
- Status: [ ] Not Started
- Estimated: 2-3 days
- Blocked by: #101

### Slice 3: Developer Response
- Issue: #103 - Collect developer response
- Status: [ ] Not Started
- Estimated: 2-3 days
- Blocked by: #102

### Slice 4 (Optional): History
- Issue: #104 - Decision history tracking
- Status: [ ] Not Started
- Estimated: 1-2 days
- Blocked by: #101-#103

## Success Criteria

- [ ] All required slices (1-3) merged
- [ ] End-to-end workflow works (developer marks decision → agent pauses → developer responds → agent resumes)
- [ ] All tests pass
- [ ] No performance regressions
- [ ] Code reviewed and approved

## Timeline

- **Phase 1:** #101 (1-2 days)
- **Phase 2:** #102, #103 in parallel (2-3 days)
- **Phase 3:** #104 optional (1-2 days)
- **Total:** 5-7 days

## Related Documents

- PRD: [Agent Clarification Requests PRD](../docs/prd/agent-clarification.md)
- Decomposition Plan: [DECOMPOSITION.md](./DECOMPOSITION.md)
- Patterns: [Vertical Slicing](../patterns/vertical-slicing.md), [Tracer Bullets](../patterns/tracer-bullets.md), [TDD](../patterns/test-driven-development.md)
```

---

## For AI Agents

### What This Playbook Tells You

1. **How to approach work:** Each issue is a vertical slice—touch all layers, end-to-end
2. **What order to work:** Dependencies are explicit; work independent issues first
3. **How much to do:** Each issue is ~1-3 days; if larger, it needs more slicing
4. **When you're done:** Acceptance criteria are defined; verify all are met

### How to Use Decomposed Issues

1. Pick the first issue with no dependencies
2. Read the issue, understand acceptance criteria
3. Review linked patterns
4. Implement end-to-end (all layers)
5. Write tests as you go (TDD)
6. Verify all acceptance criteria before finishing
7. Move to next issue

### If an Issue Is Unclear

- Ask for clarification on acceptance criteria, not on how to implement
- Flag unclear dependencies
- Don't guess on scope or approach; ask

---

## Integration with Software Development Process

This playbook supports **Phase 3 (Decomposition)** of the [Software Development Process](../processes/software-development.md#phase-3-decomposition).

It transforms:
- **Input:** PRD (from Phase 2)
- **Output:** Implementation issues ready for Phase 4

---

## Common Pitfalls

| Pitfall | What Goes Wrong | Solution |
|---------|-----------------|----------|
| **Vertical Slicing Misunderstood** | Issues slice by layer (frontend, backend, database) instead of by feature | Ensure each issue touches ALL relevant layers; verify end-to-end flow |
| **Dependencies Not Mapped** | Team starts work in wrong order, gets blocked | Make dependencies explicit; use GitHub dependency links |
| **Acceptance Criteria Vague** | Team finishes "close enough" but not actually done | Make criteria testable and specific |
| **No Integration Testing** | Slices work independently but don't integrate | Add integration test slice or acceptance criterion that verifies integration |
| **Over-Specified Issues** | Developers have no choices; creativity constrained | Specify WHAT, not HOW; trust implementers to choose good solutions |
| **Issues Too Large** | Can't complete in reasonable time | Split further; identify intermediate testable milestones |
| **Nice-to-Have Confusion** | Team spends time on optional work while blockers remain | Clearly mark optional/nice-to-have; defer until required work done |

---

## Related Patterns and Playbooks

- **Pattern:** [Vertical Slicing](../patterns/vertical-slicing.md) — Detailed guidance on decomposing into end-to-end slices
- **Pattern:** [Tracer Bullets](../patterns/tracer-bullets.md) — Implementing each slice minimally, then refining
- **Pattern:** [Feedback Loops](../patterns/feedback-loops.md) — Gathering feedback on slices before completing all
- **Process:** [Software Development Process - Phase 3 (Decomposition)](../processes/software-development.md#phase-3-decomposition) — Where this fits
- **Playbook:** [Create PRD](create-prd.md) — How to create the requirements this decomposition builds on

---

## References

- [Vertical Slicing Pattern](../patterns/vertical-slicing.md) - Detailed explanation of slice characteristics
- [Tracer Bullets Pattern](../patterns/tracer-bullets.md) - How to implement slices iteratively
- [Software Development Process](../processes/software-development.md) - Full end-to-end workflow
- GitHub Issues best practices for decomposition
