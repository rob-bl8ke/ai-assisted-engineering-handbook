# Software Development Process

The foundational end-to-end workflow for AI-assisted software engineering, refined through community experience and best practices.

This document describes the complete process from initial concept through delivery and feedback. **Human judgement and deliberate decision points are first-class elements**, not afterthoughts.

---

## Process Overview

```
    ┌─────────────────────────────────────────────────────────┐
    │                   DISCOVERY PHASE                       │
    │         (Problem understanding & alignment)             │
    └─────────────────┬───────────────────────────────────────┘
                      │
                      ▼
    ┌─────────────────────────────────────────────────────────┐
    │                  DESIGN PHASE                           │
    │         (Architecture & formal specification)           │
    └─────────────────┬───────────────────────────────────────┘
                      │
                      ▼
    ┌─────────────────────────────────────────────────────────┐
    │              DECOMPOSITION PHASE                        │
    │      (Breaking work into focused, testable units)       │
    └─────────────────┬───────────────────────────────────────┘
                      │
                      ▼
    ┌─────────────────────────────────────────────────────────┐
    │             IMPLEMENTATION PHASE                        │
    │     (AI-assisted coding with test-driven development)   │
    └─────────────────┬───────────────────────────────────────┘
                      │
                      ▼
    ┌─────────────────────────────────────────────────────────┐
    │               VALIDATION PHASE                          │
    │     (Review, quality assurance, and feedback loops)     │
    └─────────────────┬───────────────────────────────────────┘
                      │
                      ▼
    ┌─────────────────────────────────────────────────────────┐
    │              RESOLUTION PHASE                           │
    │   (Integration, deployment, and follow-up planning)     │
    └─────────────────────────────────────────────────────────┘
```

---

## Phase 1: Discovery

**Goal:** Establish shared understanding of the problem before designing solutions.

### 1.1 Initial Brief or Idea

**Entry Point:** You have a concept, requirement, or problem statement.

- A feature idea
- A bug report
- A technical debt item
- A refactoring opportunity
- A performance issue

**🟡 Human Judgement Point:** Does this warrant formal documentation?

- **Minor, well-defined tasks** → Skip to lightweight work items (→ Phase 2)
- **Significant or novel work** → Proceed to discovery (→ 1.2)

### 1.2 Shared Understanding (Grill Me)

**Activity:** Use AI-assisted inquiry to explore and align on requirements.

**See Also:**
- [Grill Me Skill](../skills/grill-me.md) — Executable specification for the discovery conversation
- [Repository Exploration Playbook](../playbooks/repository-exploration.md) — If this is about understanding an existing codebase, use this playbook to systematically gather context

**Approach:**
- Document initial assumptions
- Ask clarifying questions (via AI or human discussion)
- Uncover hidden constraints and dependencies
- Identify unclear terminology
- Surface conflicting perspectives

**Outcomes:**
- Clear problem statement
- Documented constraints
- List of stakeholder concerns
- Agreed-upon success criteria

**🟡 Human Judgement Point:** Is the understanding sufficient?

- **Gaps remain** → Iterate discovery (return to 1.2)
- **Sufficiently clear** → Proceed to design (→ Phase 2)

### 1.3 Rainy Path: Discovery Blockers

**Scenario:** The problem is poorly defined, contradictory, or depends on external information.

**Approach:**
- Identify specific information gaps
- Define what information is needed and how to obtain it
- Create investigation tasks
- Schedule follow-up discussion

**Resolution:**
- Either resolve the blocker and return to 1.2
- Or defer work until blockers are removed
- Document the blocker explicitly in the issue tracker

---

## Phase 2: Design

**Goal:** Formalize the solution approach and constraints.

### 2.1 Lightweight Work Items

**When:** For well-understood, straightforward tasks that don't require extensive design.

**Activity:**
- Write a brief work description
- List success criteria
- Identify acceptance tests
- Note any dependencies

**Outcomes:**
- Minimal but clear specification
- Testable success criteria
- Proceed to decomposition (→ Phase 3)

### 2.2 Formal PRD (Product Requirements Document)

**When:** For significant features, novel work, or changes affecting multiple systems.

**See Also:**
- [Write PRD Skill](../skills/write-prd.md) — Executable specification for writing formal requirements
- [Create PRD Playbook](../playbooks/create-prd.md) — Detailed step-by-step guidance on writing a complete PRD that communicates requirements clearly to both humans and AI agents

**Activity:**
- Define the problem being solved
- Articulate the business case or motivation
- Specify functional requirements
- Document non-functional requirements (performance, security, scalability)
- Identify constraints and dependencies
- Define acceptance criteria
- Outline success metrics
- Note alternative approaches considered and rejected

**Outcomes:**
- Complete, reviewable specification
- Explicit design decisions and rationale
- Clear scope boundaries

**🟡 Human Judgement Point:** Does the PRD represent the best approach?

- **Revisions needed** → Iterate on design (return to 2.2)
- **Approved** → Proceed to decomposition (→ Phase 3)

### 2.3 Rainy Path: Design Conflicts

**Scenario:** Design review reveals conflicts, contradictions, or missing requirements.

**Approach:**
- Document the conflict explicitly
- Identify affected stakeholders
- Propose resolution options with tradeoffs
- Schedule discussion or decision-making

**Resolution:**
- Revise design based on decision
- Return to design review (2.2)
- Or escalate if decision-making authority is unclear

---

## Phase 3: Decomposition

**Goal:** Break work into focused, vertically-sliced, independently-testable units.

### 3.1 Identify Slices Using Vertical Slicing

**Activity:**
- Analyze the design
- Identify vertical slices (end-to-end features that span all layers)
- See [Vertical Slicing Pattern](../patterns/vertical-slicing.md) for detailed guidance on how to slice work
- See [PRD to Issues Skill](../skills/prd-to-issues.md) for executable specification of decomposition
- See [Decompose Work Playbook](../playbooks/decompose-work.md) for step-by-step process to convert a PRD into implementation issues

**Approach:**
- Start with the thinnest possible slice
- Aim for slices small enough to complete in 1-4 hours
- Avoid creating dependencies between slices (or make dependencies explicit)
- Each slice should be:
  - **Independently testable** — Can be tested in isolation
  - **Independently reviewable** — A single person can understand it completely
  - **Independently deployable** — Can be merged and deployed without others
  - **Valuable** — Provides some benefit or unblocks other work

**Outcomes:**
- Ordered list of implementation issues
- Each issue is clear enough that an AI agent can work independently on it
- Dependencies between issues are documented

**🟡 Human Judgement Point:** Can decomposition be simpler or more independent?

- **Slices are coupled** → Redesign decomposition (return to 3.1)
- **Slices are well-sliced** → Proceed to implementation (→ Phase 4)

### 3.2 Rainy Path: Impossible Decomposition

**Scenario:** The work cannot be meaningfully sliced, or slices have unavoidable dependencies.

**Approach:**
- Consider investigation or spike work to reduce uncertainty
- Identify what information would allow better decomposition
- Create focused investigation issues
- Resolve dependencies first, then re-decompose

**Resolution:**
- Complete investigation work
- Return to decomposition (3.1)
- Or acknowledge that work is inherently sequential and proceed with full understanding of the cost

---

## Phase 4: Implementation

**Goal:** Write well-tested code following the agreed-upon design.

### 4.1 Implementation Approach

**🟡 Human Judgement Point:** Is unattended execution appropriate?

**Option A: AFK (Away From Keyboard) Workflow — Autonomous AI Execution**
- AI agent works independently with predefined success criteria
- Minimal human intervention
- Suitable for:
  - Well-understood, well-scoped work
  - Straightforward technical tasks
  - High confidence in the design
  - When you have other work to do
- Proceed to 4.2

**Option B: Supervised Implementation — Step-by-Step Collaboration**
- Iterative back-and-forth between human and AI
- Human reviews each step before proceeding
- Suitable for:
  - Novel or complex work
  - Areas with high business impact
  - When learning/understanding is important
  - When tradeoffs need human input
- Proceed to 4.3

### 4.2 AFK Implementation (Ralph Loop)

**Approach:**
- AI agent receives:
  - Complete issue specification
  - Acceptance criteria
  - Links to relevant code and documentation
  - Definition of done
- Agent implements the complete slice using:
  - **[Test-Driven Development (TDD)](../patterns/test-driven-development.md)** — Write tests first, then code to pass them
  - **[Tracer Bullets](../patterns/tracer-bullets.md)** — Build a minimal end-to-end path first, then refine
  - [Test-Driven Development](../patterns/test-driven-development.md) ensures all acceptance criteria are met
  - Code follows established patterns
  - Documentation updated
- No human intervention required unless blockers arise

**Blockers:**
- If the agent encounters ambiguity, it documents the question and pauses
- Human reviews and clarifies
- Agent continues from clarification

**Outcomes:**
- Complete implementation
- Full test coverage
- Commit history explaining decisions
- Ready for review (→ Phase 5)

**See Also:**
- **[Ralph Loop Pattern](../patterns/ralph-loop/README.md)** — Canonical pattern for autonomous agent implementation loops with test-driven development
- **[Test-Driven Development (TDD)](../patterns/test-driven-development.md)** — Testing and implementation methodology
- **[Tracer Bullets](../patterns/tracer-bullets.md)** — Minimal end-to-end implementation strategy

### 4.3 Supervised Implementation

**Approach:**
- Break the issue into smaller steps
- For each step:
  - Human and AI discuss approach
  - AI implements the step
  - Human reviews and discusses results
  - Agree on next step
- Continue until complete

**Advantages:**
- Opportunity to learn from AI reasoning
- Course-correct early if needed
- Shared ownership of decisions

**Outcomes:**
- Complete implementation
- Documented reasoning for key decisions
- Ready for review (→ Phase 5)

### 4.4 Rainy Path: Blocked Implementation

**Scenario:** The implementation is blocked by:
- Unclear requirements
- Conflicting designs
- Missing dependencies
- Technical impossibilities
- Ambiguous acceptance criteria

**Approach:**
- Document the blocker explicitly
- Return to the relevant earlier phase:
  - Discovery issues → Phase 1 or 2
  - Design issues → Phase 2 or 3
  - Decomposition issues → Phase 3
- Resolve the blocker
- Resume implementation from where it was blocked

**Prevention:**
- Thorough design review before implementation
- Explicit acceptance criteria
- Clear architectural decisions

---

## Phase 5: Validation

**Goal:** Verify the implementation meets requirements and doesn't introduce regressions.

### 5.1 Implementation Review

**Activity:** Code review by a human familiar with the codebase and design.

**See Also:** [Review Implementation Playbook](../playbooks/review-implementation.md) — Comprehensive guidance on systematic code review, including prioritizing bugs, risks, and test coverage.

**Reviewer checks:**
- ✓ All acceptance criteria are met
- ✓ Code follows established patterns and conventions
- ✓ Tests are comprehensive and meaningful
- ✓ No obvious bugs or security issues
- ✓ Performance is acceptable
- ✓ Documentation is updated
- ✓ Commit messages explain decisions

**🟡 Human Judgement Point:** Is the implementation acceptable?

- **Revisions needed** → Request changes
  - AI or human addresses feedback
  - Return to review (5.1)
- **Approved** → Proceed to 5.2

### 5.2 Fresh-Context Review

**Activity:** Independent review by someone (preferably human) who was not involved in the implementation or prior reviews.

**See Also:** [Repository Exploration Playbook](../playbooks/repository-exploration.md) — Reviewers can use this playbook to quickly understand context if unfamiliar with the codebase.

**Rationale:** Fresh perspective catches:
- Unclear assumptions
- Missing documentation
- Architectural concerns
- Integration issues
- User experience problems

**🟡 Human Judgement Point:** Does the work integrate cleanly?

- **Integration issues** → Address concerns
  - Return to implementation (Phase 4) or design (Phase 2)
- **Approved** → Proceed to 5.3

### 5.3 Quality Assurance

**Activity:** Test the implementation:
- ✓ Manual testing of happy path
- ✓ Testing of error cases
- ✓ Integration with adjacent systems
- ✓ Performance under load (if applicable)
- ✓ Security review (if applicable)
- ✓ Regression testing of related features

**🟡 Human Judgement Point:** Does the implementation meet quality standards?

- **Issues found** → Return to implementation (Phase 4)
- **Passes QA** → Proceed to Phase 6

### 5.4 Rainy Path: Failed Validation

**Scenario:** Issues are discovered that:
- Violate acceptance criteria
- Introduce regressions
- Don't meet quality standards
- Expose design flaws

**See Also:** [Investigate Bug Playbook](../playbooks/investigate-bug.md) — Systematic approach to understanding root cause of bugs, from reproduction through fix and prevention.

**Approach:**
- Categorize the issue:
  - **Implementation bug** → Return to Phase 4; use Investigate Bug playbook for root cause analysis
  - **Design issue** → Return to Phase 2
  - **Scope change** → Create new issues (→ Phase 6.3)
  - **Requirements clarification** → Return to Phase 1

**Prevention:**
- Comprehensive acceptance criteria
- Thorough testing during implementation
- Regular review checkpoints in supervised workflows

---

## Phase 6: Resolution

**Goal:** Integrate the work, plan follow-up activities, and close the loop.

### 6.1 Integration and Deployment

**Activity:**
- Merge to main development branch
- Deploy to appropriate environment (dev, staging, production)
- Monitor for issues
- Notify stakeholders

**🟡 Human Judgement Point:** Is the deployment successful?

- **Deployment issues** → Address quickly
  - Minor: patch and redeploy (Phase 4)
  - Major: rollback and reassess (Phase 2)
- **Successful** → Proceed to 6.2

### 6.2 Team Review and Feedback

**Activity:**
- Share implementation with broader team
- Collect feedback
- Document lessons learned
- Identify best practices or patterns worth formalizing

**Types of feedback:**
- Positive: What worked well?
- Corrective: What didn't work?
- Prescriptive: How could this be better?
- Generalizable: What pattern could we reuse?

### 6.3 Rainy Path: New Work Discovered

**Scenario:** During implementation or review, new work is discovered:
- Adjacent features that should be built
- Technical debt exposed
- Edge cases not handled
- Refactoring opportunities
- Documentation gaps

**Approach:**
- Document each item as a new issue
- Link to the completed work
- Prioritize relative to other work
- Decide which to include in immediate follow-up vs. backlog

**Examples:**
- "Support pagination for list feature" (new issue)
- "Add caching for performance" (new issue)
- "Document the search algorithm" (new issue)

### 6.4 Process Retrospective (Optional)

**When:** For significant work or when process issues were encountered.

**Activity:**
- What worked well in the process?
- Where did we get stuck?
- What should we do differently next time?
- Did the design hold up or did we discover issues?
- Was the decomposition effective?

**Outcomes:**
- Improvements to the handbook
- Updated patterns or playbooks
- Better examples for future similar work

---

## Rainy Paths: Cross-Cutting Issues

### Escalation

**When:** A decision requires higher authority or expertise outside the current team.

**Approach:**
- Document the decision needed
- List options with tradeoffs
- Identify who needs to decide
- Pause work while decision is made
- Resume from the decision point

### Blocked Dependencies

**When:** Progress is blocked by work outside this issue.

**Approach:**
- Identify the dependency explicitly
- Determine if the dependency can be:
  - Completed first (serial path)
  - Worked around (concurrent path)
  - Eliminated (redesign)
- Communicate the blocker to dependent work
- Unblock and resume

### Scope Creep

**When:** New requirements emerge during work.

**Approach:**
- Document the new requirement
- Assess impact on current slice:
  - Small impact → Include in current work
  - Large impact → Create new issue
- Discuss with stakeholders
- Decide whether to expand or defer
- Update issue scope if expanded
- Create new issue if deferred

---

## Human Judgement Points Summary

This process succeeds because it identifies **explicit decision points** where human judgment is required:

| Phase | Decision | Options |
|-------|----------|---------|
| **Discovery** | Sufficient understanding? | Iterate discovery ↔ Proceed to design |
| **Design** | Design acceptable? | Iterate design ↔ Proceed to decomposition |
| **Decomposition** | Slices well-formed? | Iterate slices ↔ Proceed to implementation |
| **Implementation** | Supervised or AFK? | Supervised workflow ↔ AFK workflow |
| **Review** | Implementation acceptable? | Request revisions ↔ Proceed to fresh review |
| **Fresh Review** | Integration acceptable? | Address issues ↔ Proceed to QA |
| **QA** | Quality acceptable? | Return to implementation ↔ Proceed to deployment |
| **Deployment** | Deployment successful? | Troubleshoot ↔ Proceed to team review |

**Key principle:** Rather than trying to automate these decisions away, the process makes them explicit and gives AI clear guidance on when to escalate.

---

## Linking Back to Root Navigation

This process is introduced in the root [README.md](../README.md) via the "Getting Started" decision tree:

```
I have work to do
    ↓
[Is there understanding?] → NO → [Grill Me (Discovery)]
    ↓ YES
[Significant design?] → YES → [Create PRD (Design)]
    ↓ NO → [Lightweight work item]
[Can be vertically sliced?] → YES → [Produce issues (Decomposition)]
    ↓ NO → [Investigation/de-risking]
[Unattended execution?] → YES → [AFK workflow (Implementation 4.2)]
    ↓ NO → [Supervised workflow (Implementation 4.3)]
    ↓
[Review → QA → Feedback (Validation & Resolution)]
```

Each phase of this process corresponds to one or more nodes in the root decision tree. The README navigates developers to this full process documentation.

---

## Process Variants

This core process can be adapted for specific scenarios:

- **Bug Fixing:** Start at Discovery with a clear problem statement; design is usually lightweight
- **Refactoring:** Design focuses on behavioral equivalence; implementation emphasizes test coverage
- **Performance Optimization:** Design includes performance targets; QA emphasizes measurement
- **Unfamiliar Codebase:** Extend Discovery with exploration; decomposition is minimal initially
- **Maintenance Tasks:** Lightweight design; straightforward implementation; minimal validation overhead

Future handbook documentation will expand these variants as separate processes.

---

## Key Patterns Used in This Process

This process incorporates several key patterns from the handbook. Understanding these patterns will help you execute the process more effectively:

### Core Patterns

- **[Vertical Slicing](../patterns/vertical-slicing.md)** — Breaking work into independently-deployable units (Phase 3)
  - Ensures parallel work
  - Each slice is testable end-to-end
  - Early feedback on approach

- **[Tracer Bullets](../patterns/tracer-bullets.md)** — Minimal end-to-end implementation (Phase 4)
  - Build thin paths first
  - Add refinements incrementally
  - Prove approach works early

- **[Test-Driven Development (TDD)](../patterns/test-driven-development.md)** — Write tests first, then code (Phase 4)
  - Red → Green → Refactor cycle
  - Clear success criteria
  - Confidence in correctness

- **[Context Management](../patterns/context-management.md)** — Provide focused information (all phases)
  - Give AI exactly what it needs
  - Avoid context overload
  - Refresh context as work progresses

- **[Feedback Loops](../patterns/feedback-loops.md)** — Learn from each slice and apply improvements (all phases)
  - Fast feedback cycles (hours, not weeks)
  - Each slice shapes the next
  - Continuous improvement

### How Patterns Work Together

```
Vertical Slicing (how to break work)
    ↓
Each slice is implemented with Tracer Bullets (minimal end-to-end)
    ↓
Using Test-Driven Development (red → green → refactor)
    ↓
With Context Management (focused information)
    ↓
Creating Feedback Loops (learn and improve)
    ↓
Next slice is better informed
```

---

## References

- **Principles:** Separation of concerns, human judgment, explicit escalation
- **Related handbook entries:** (Future cross-links)
  - Pattern: Vertical Slicing
  - Pattern: Test-Driven Development
  - Pattern: Ralph Loops
  - Principle: Human-in-the-Loop
  - Principle: Fresh-Context Review

---

**Last Updated:** 2026-08-16  
**Status:** Core Process Documentation (MVP)
