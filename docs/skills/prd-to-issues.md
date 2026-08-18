# Skill: PRD to Issues

## Overview

**PRD to Issues** is an automated or guided skill that transforms a formal Product Requirements Document or specification (output from [Write PRD / To Spec](write-prd.md)) into a prioritized, sequenced list of implementation issues using vertical slicing principles. Each issue is independently implementable, end-to-end testable, and sized for a single development context.

**Canonical Purpose:** Break a complex PRD into a sequence of thin, vertical slices (tracer bullets) that can be implemented independently, each moving toward complete functionality, minimizing dependencies, and enabling parallel work or AFK (away from keyboard) agent execution.

**Related Process Phase:** [Software Development Process, Phase 3: Decomposition](../processes/software-development.md#phase-3-decomposition)

**Detailed How-To:** [Decompose Work Playbook](../playbooks/decompose-work.md) (for step-by-step guidance on creating vertical slices and decomposing into issues)

---

## Canonical Skill Behavior

### Entry Conditions

You have:
- Approved PRD with clear requirements and acceptance criteria
- Or an approved specification created by [Write PRD / To Spec](write-prd.md)
- Understanding of existing codebase and system architecture
- Estimation capacity (understanding of team velocity or individual developer capacity)
- Access to issue tracking system (GitHub Issues, Jira, etc.)

### Core Decision Points

**Decision 1: Understand the PRD and Identify Core Features**
- What are the major feature groups in the PRD?
- Which feature is the thinnest slice that demonstrates complete value?
- What are the dependencies between features?
- Can features be implemented independently or are they blocked?
- **Document feature breakdown and dependency map**

**Decision 2: Identify Vertical Slices (Tracer Bullets)**
- What is the absolute minimal end-to-end behavior that delivers value?
- Can this be implemented in 1-3 days (single context window)?
- Does this slice touch all layers (schema, API, UI, tests)?
- **Define the first slice carefully; it sets the pattern**

**Decision 3: Define Subsequent Slices**
- What is the next minimal feature that builds on slice #1?
- What features are independent of earlier slices?
- Can independent slices be worked in parallel?
- What's the optimal sequence to minimize blockers?
- **Order slices to maximize parallelizability**

**Decision 4: Create Individual GitHub Issues**
- For each slice, create one issue with:
  - Clear title (what is being implemented)
  - Description (why this slice, what it enables)
  - Acceptance criteria (how to verify it works)
  - Links to parent PRD and related issues
  - Blocked-by relationships (what must complete first)
  - Definition of Done (testing, documentation, review)
- **Ensure each issue can be understood and implemented independently**

**Decision 5: Verify Slice Quality**
- Can each issue be implemented without asking more questions?
- Is each issue sized to fit in a single context window (1-3 days)?
- Does each issue have clear acceptance criteria?
- Are dependencies explicitly documented?
- Will completing each issue leave the codebase in a green, deployable state?
- **If any issue fails quality checks: Split or clarify**

**Decision 6: Create Tracking and Sequencing**
- Create a parent issue or GitHub project that ties all slices together
- Document the sequence and rationale
- Mark dependent issues with explicit GitHub blocking relationships
- Create DECOMPOSITION.md document showing the decomposition strategy
- **Enable implementation team to understand the big picture**

**Decision 7: Review and Approval**
- Product owner reviews issue descriptions for accuracy
- Technical lead reviews for feasibility and sizing
- Implementation team confirms they can work independently
- Any objections or concerns are resolved
- **Proceed to implementation only after approval**

### Skill Outputs

Upon successful completion, you should have:

1. **Ordered List of GitHub Issues**
   - Minimum 1-3 dependent slices; maximum 5-10 (manage complexity)
   - Each issue has:
     - Title: [ACTION] [WHAT] (e.g., "Add user to wishlist on item view")
     - Description: Context, why this slice, what it enables
     - Acceptance Criteria: Specific, testable behaviors
     - Acceptance Criteria must include each requirement from PRD that this issue covers
     - Definition of Done: Tests, documentation, review requirements
     - Blocked by: Links to issues that must complete first
     - Links to: PRD document and related playbooks

2. **GitHub Dependency Graph**
   - Issues linked with blocking relationships
   - Clear dependency visualization
   - Independent issues identifiable for parallel work

3. **DECOMPOSITION.md Document**
   - Overview of the decomposition strategy
   - Explanation of why slices were chosen this way
   - Sequence rationale
   - Dependency map (can be Mermaid diagram)
   - Links to all related issues
   - Tracking table: (Issue | Status | Blocked By | Enables)

4. **Vertical Slice Template** (for reference)
   - Shows what each issue should look like
   - Guides consistency across all issues
   - Can be reused for future decompositions

### Skill Verification Checklist

- [ ] All PRD requirements are covered by at least one issue
- [ ] Each issue is independently implementable (can start without waiting for others)
- [ ] Each issue is sized to fit in 1-3 days / single context window
- [ ] Each issue touches all layers (schema/API/UI/tests, if applicable)
- [ ] Issue titles are clear action-oriented descriptions
- [ ] Acceptance criteria are specific and testable
- [ ] Acceptance criteria are phrased as behavior (not implementation)
- [ ] Each acceptance criterion from PRD is mapped to an issue
- [ ] Blocked-by relationships are accurately documented
- [ ] Issues can be worked in sequence or in parallel (depending on blockers)
- [ ] PRD questions and ambiguities are resolved before creating issues
- [ ] Implementation team can implement each issue without asking clarifying questions
- [ ] Testing strategy from PRD is reflected in each issue's acceptance criteria
- [ ] Decomposition strategy is documented (DECOMPOSITION.md)
- [ ] Product owner and tech lead approve the decomposition

---

## Decision Flow

```
START: Approved PRD with Clear Requirements
│
├─ Understand PRD and identify core features
│  ├─ Features listed and prioritized? → Continue
│  ├─ Dependencies mapped? → Continue
│  └─ Thinnest slice identified? → Continue
│
├─ Identify first vertical slice (tracer bullet)
│  ├─ Minimal end-to-end value? → Continue
│  ├─ Fits in 1-3 days? → Continue
│  ├─ Touches all layers? → Continue
│  └─ Can be implemented independently? → Continue
│
├─ Define subsequent slices
│  ├─ Each slice builds on previous or is independent? → Continue
│  ├─ Sequence minimizes blockers? → Continue
│  └─ Parallel slices identified? → Continue
│
├─ Create GitHub issues for each slice
│  ├─ All PRD requirements covered? → Continue
│  ├─ Each issue has acceptance criteria? → Continue
│  ├─ Each issue has Definition of Done? → Continue
│  └─ Blocked-by relationships explicit? → Continue
│
├─ Verify slice quality
│  ├─ Each issue independently implementable? → Continue
│  ├─ Each issue sized correctly? → Continue
│  ├─ No ambiguities in acceptance criteria? → Continue
│  └─ Will each issue leave codebase in green state? → Continue
│
├─ Create tracking and sequencing
│  ├─ Parent issue created? → Continue
│  ├─ GitHub dependencies configured? → Continue
│  └─ DECOMPOSITION.md documented? → Continue
│
├─ Review and approval
│  ├─ Product owner approves? → Continue
│  ├─ Tech lead approves? → Continue
│  └─ Implementation team confirms readiness? → Continue
│
└─ → READY FOR IMPLEMENTATION PHASE
```

---

## When NOT to Use This Skill

- **Trivial work:** If PRD can be implemented as a single issue, skip issue creation ceremony
- **Already decomposed:** If PRD is already broken into issues, skip to implementation
- **Exploratory work:** Spikes and prototypes may use different issue-creation strategies
- **Urgent fire-fighting:** Production incidents may use lighter-weight issue creation

---

## Harness-Specific Implementation Notes

### For AI Agents

**Behavior:**
- Read and analyze the PRD systematically
- Identify features and dependencies
- Propose vertical slices and present to user for feedback
- Create GitHub issues programmatically via API
- Generate DECOMPOSITION.md document
- Set up blocking relationships

**Decision Rules:**
- If a slice is too large (>3 days of work): Split it smaller
- If a slice has many dependencies: Reorder to reduce blockers
- If acceptance criteria are vague: Refine based on PRD requirements
- If blockers are unclear: Ask implementation team for feasibility input

**Questions to Ask User:**
- "Should these features be independent or sequenced?"
- "Can we implement [feature A] before [feature B]?"
- "Is this issue sized correctly for your context window?"
- "Do we need to add any preparatory issues (prefactoring, dependencies)?"

**Termination Criteria:**
- All issues created and linked
- Blocking relationships configured
- DECOMPOSITION.md generated
- Product owner and tech lead approve decomposition
- Ready to hand off to implementation team

**Output Format:**
- Create issues in GitHub (or export as importable format)
- Generate DECOMPOSITION.md as a navigable Markdown document
- Provide summary of slice sequence and rationale

**Integration:**
- Create parent issue/epic linking all slices
- Update README or project documentation with link to decomposition
- Transition to implementation phase or notify team that work is ready

### For Humans Using This Skill

**Facilitation Approach:**
- Work with product owner, technical lead, and implementation team
- Use this skill to structure the decomposition discussion
- Iterate on slice proposals with technical team

**When to Involve AI:**
- Use AI to draft initial slice proposals based on PRD
- Use AI to create GitHub issues from slice descriptions
- Use AI to identify dependencies and create blocking relationships
- Use AI to generate DECOMPOSITION.md documentation

**Review Process:**
1. Product owner reviews slices for business logic completeness
2. Technical lead reviews for feasibility and sizing
3. Implementation team reviews for clarity and independence
4. Return to author for revisions if needed
5. Final approval before implementation starts

---

## Relationship to Patterns

This skill operationalizes:
- **[Vertical Slicing](../patterns/vertical-slicing.md):** Breaking PRD into thin, end-to-end, independently implementable pieces
- **[Tracer Bullets](../patterns/tracer-bullets.md):** Implementing the thinnest end-to-end slice first to establish patterns
- **[Feedback Loops](../patterns/feedback-loops.md):** Each slice gets reviewed before proceeding to next (incremental validation)

---

## Relationship to Process

This skill is the **canonical implementation** of Process Phase 3 (Decomposition). It breaks a formal specification into executable work units.

When PRD to Issues is complete, the process moves to:
- **Phase 4 (Implementation):** Implementation team works on issues in sequence or parallel
- **Human Judgement Point:** Are issues clear and ready for implementation?

---

## Skill Maturity Criteria

This skill is considered mature because:
- ✅ Canonical behavior is clearly defined (7 decision points, verification checklist)
- ✅ Success criteria are measurable (checklist items can be verified)
- ✅ It operationalizes a mature process phase (Phase 3)
- ✅ It's harness-agnostic (works with any issue tracker, any team)
- ✅ Output is tool-independent (Markdown tracking, standard GitHub issues)
- ✅ Both humans and AI agents can execute it

---

## Common Pitfalls

| Pitfall | How to Recognize | How to Fix |
|---------|-------------------|-----------|
| **Horizontal slicing** | Each issue handles only one layer (all DB, all UI, all API) | Rethink issues as vertical: each touches all layers for a complete feature |
| **Oversized issues** | Issue description takes > 2000 words; acceptance criteria are 20+ items | Split into smaller slices; each issue should fit in 1-3 days |
| **Undersized issues** | Issue has only 1-2 acceptance criteria; feels like micro-tasks | Combine related micro-tasks into meaningful slices |
| **Vague acceptance criteria** | Criteria say "should be efficient" or "looks right" | Make specific and measurable: "API response < 200ms (p95)" |
| **Missing dependencies** | Issue assumes another issue is done but doesn't explicitly block | Review all dependencies; set GitHub blocking relationships |
| **Ambiguous for implementation team** | Implementation team asks clarifying questions that PRD should have answered | Go back to PRD; clarify ambiguities before re-decomposing |
| **Tightly coupled slices** | Almost every issue blocks multiple others; can't be parallelized | Reorder and refactor to reduce coupling |
| **Skipping traceability** | Issues don't link back to PRD; no DECOMPOSITION.md | Add links to PRD in each issue; create DECOMPOSITION.md tracking matrix |
| **No testing strategy per issue** | Issues have acceptance criteria but no "how to verify" | Add Definition of Done with testing requirements to each issue |

---

## Vertical Slice Template

```markdown
## [Issue Title: ACTION + WHAT]

**Related to PRD:** [Link to PRD document]

**Slice Sequence:** [Position in decomposition, e.g., "Slice 1 of 5"]

**Blocked By:** [List issues that must complete first, or "None"]

**Enables:** [List issues that depend on this one completing]

### What This Slice Delivers

[One paragraph explaining what this slice accomplishes end-to-end, why it matters, what it enables]

### Acceptance Criteria

- [ ] [Specific, observable, testable behavior from PRD]
- [ ] [Specific, observable, testable behavior from PRD]
- [ ] [User workflow validation]
- [ ] [Error case handling]

### Approach

[Technical guidance for implementation, e.g., which patterns apply, which parts of codebase to touch, any prefactoring needed]

### Definition of Done

- [ ] Code implements all acceptance criteria
- [ ] Tests written for happy path and error cases
- [ ] Acceptance criteria verified (manual or automated)
- [ ] Code review completed and approved
- [ ] Documentation updated (if applicable)
- [ ] No regressions in existing tests
- [ ] Codebase remains deployable

### Notes

[Any additional context, ambiguities to resolve with team, risks to mitigate]

```

---

## Examples

### Example 1: Wishlist Feature Decomposition

**PRD Requirements:**
- Users can add items to wishlist
- Wishlist syncs across devices
- Users can view wishlist offline
- Users can manage wishlist (delete items, clear all)

**Decomposition:**

1. **Slice 1: Add item to wishlist locally** (Blocked by: None)
   - What: User can tap "Add to Wishlist" and see item in local wishlist list
   - Enables: Slice 2 (sync) and Slice 4 (offline)
   - Touches: UI, local storage, tests

2. **Slice 2: Sync wishlist to backend** (Blocked by: Slice 1)
   - What: Wishlist changes sync to server; persists across device restarts
   - Enables: Slice 3 (multi-device)
   - Touches: UI, API, database, tests

3. **Slice 3: Multi-device sync** (Blocked by: Slice 2)
   - What: Wishlist updates on device A appear on device B within 5 seconds
   - Enables: Slice 5 (management)
   - Touches: Websockets, sync logic, tests

4. **Slice 4: Offline wishlist access** (Blocked by: Slice 1)
   - What: Users can view saved wishlist items without internet connection
   - Enables: (independent)
   - Touches: Cache strategy, offline UI, tests

5. **Slice 5: Wishlist management** (Blocked by: Slice 2)
   - What: Users can delete items from wishlist; users can clear all items
   - Enables: (independent)
   - Touches: UI, API, database, tests

**Parallelizable:** Slices 3, 4, 5 can be worked in parallel after Slice 1-2 complete

---

### Example 2: Dashboard Performance Fix Decomposition

**PRD Requirements:**
- Dashboard loads in < 2 seconds (p95)
- All UI interactions responsive in < 200ms
- API calls optimized (no N+1 queries)
- Performance metrics tracked and monitored

**Decomposition:**

1. **Slice 1: Identify performance bottlenecks** (Blocked by: None)
   - What: Profile dashboard; identify slow queries, slow renders, network delays
   - Enables: All other slices (diagnostic data)
   - Touches: Performance profiling, logging, tests

2. **Slice 2: Optimize API queries** (Blocked by: Slice 1)
   - What: Eliminate N+1 queries; batch API calls; add caching headers
   - Enables: Dashboard performance improvement
   - Touches: API layer, query optimization, tests

3. **Slice 3: Optimize UI rendering** (Blocked by: Slice 1)
   - What: Virtualize long lists; defer non-critical renders; memoize components
   - Enables: Dashboard responsiveness
   - Touches: UI layer, rendering optimization, tests

4. **Slice 4: Implement monitoring and alerts** (Blocked by: Slice 1)
   - What: Track performance metrics; alert on regressions
   - Enables: Ongoing performance maintenance
   - Touches: Monitoring, alerting, tests

---

## Integration with Software Development Process

**When PRD to Issues Triggers:**
- Process Phase 3 (Decomposition)
- Entry criteria: Approved PRD with clear requirements

**PRD to Issues Outputs Become:**
- GitHub issues ready for implementation
- Inputs to Phase 4 (Implementation)

**Next Step After PRD to Issues:**
- If issues are approved: Implementation team begins Phase 4
- If issues need revision: Iterate decomposition (stay in Phase 3)
- If issues reveal PRD gaps: Return to Phase 2 (Write PRD) for clarification

---

## For AI Agents Implementing This Skill

### Analysis Steps

1. **PRD Analysis:** Read and extract all requirements, acceptance criteria, constraints
2. **Dependency Analysis:** Identify what must be built first, what's independent
3. **Slice Identification:** Propose 3-8 vertical slices from thinnest to most complex
4. **Issue Creation:** Draft GitHub issues from each slice
5. **Verification:** Check that all PRD requirements are covered, no issues are too large
6. **Documentation:** Generate DECOMPOSITION.md with tracking matrix

### Questions to Ask User

- "Should [feature A] and [feature B] be independent or sequenced?"
- "Is this slice sized appropriately (1-3 days)?"
- "Do we need preparatory work before starting [feature]?"
- "Can [feature] be worked in parallel with [other feature]?"

### When to Stop

Stop when:
- All issues created and linked
- All PRD requirements are covered by at least one issue
- User/product owner approves decomposition
- Implementation team confirms readiness

---

## Relationship to Other Skills

This skill builds on:
- **[Write PRD Skill](write-prd.md):** Takes formal requirements and decomposes them

This skill feeds into:
- Implementation phase (Phase 4 — individual developers or AI agents work on issues)
- **[Review Implementation Skill](../playbooks/review-implementation.md):** Reviews completed slices

Together with other skills, this enables the **Discovery → Design → Decomposition → Implementation** flow of the software development process.

---

