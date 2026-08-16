# Skill: Write PRD

## Overview

**Write PRD** is an automated or guided skill that transforms a clearly understood problem (output from Grill Me) into a formal Product Requirements Document (PRD). The PRD is a canonical specification that can be executed by implementation teams (human or AI).

**Canonical Purpose:** Create a clear, actionable specification that describes what should be built, why it matters, and what success looks like — without prescribing how to build it.

**Related Process Phase:** [Software Development Process, Phase 2: Design](../processes/software-development.md#phase-2-design)

**Detailed How-To:** [Create PRD Playbook](../playbooks/create-prd.md) (for step-by-step guidance on writing PRDs)

---

## Canonical Skill Behavior

### Entry Conditions

You have:
- Clear problem statement (output from [Grill Me skill](grill-me.md))
- Documented constraints and dependencies
- Stakeholder map with priorities
- Initial success criteria
- (Optional) Access to existing codebase or system context

### Core Decision Points

**Decision 1: Review and Confirm Problem Statement**
- Is the problem clearly stated in user terms?
- Is the impact on users/business articulated?
- Is urgency justified?
- **If gaps:** Request clarification or iterate Grill Me
- **If confirmed:** Proceed to requirements definition

**Decision 2: Define Functional Requirements**
- What must the system do? (Core features)
- What should it do? (Nice-to-have features)
- What should it NOT do? (Explicitly out of scope)
- Are requirements described in observable, testable terms?
- **If vague:** Refine to specific, observable behaviors
- **If clear:** Continue

**Decision 3: Define Non-Functional Requirements**
- What performance targets must be met? (speed, scalability, reliability)
- What security requirements exist? (authentication, authorization, data protection)
- What integration requirements exist? (APIs, external systems, protocols)
- Are these testable?
- **If missing critical requirements:** Add them
- **If complete:** Continue

**Decision 4: Design User Workflows**
- Can you describe the happy path from user perspective?
- Can you describe error paths and how the system recovers?
- Can you describe alternative workflows?
- Are workflows independent of implementation (tool/platform agnostic)?
- **If workflows are unclear:** Ask users/stakeholders to walk through scenarios
- **If clear:** Continue

**Decision 5: Identify Acceptance Criteria**
- For each major requirement, can you define measurable acceptance criteria?
- Can a tester or reviewer verify this behavior without ambiguity?
- Are acceptance criteria phrased as observable outcomes (not implementation)?
- **If criteria are implementation-specific:** Rephrase to describe behavior only
- **If clear and measurable:** Continue

**Decision 6: Surface Open Questions and Risks**
- What ambiguities remain?
- What dependencies need resolution?
- What technical challenges might the implementation team face?
- What assumptions have we made that could be wrong?
- **Document each question and its implications**

**Decision 7: Verify Cross-Functional Alignment**
- Does the PRD address stakeholder concerns identified in Grill Me?
- Has the product owner approved this specification?
- Has the technical lead reviewed for feasibility?
- Are all critical stakeholders aligned?
- **If conflicts:** Resolve before proceeding
- **If aligned:** PRD is ready for decomposition

### Skill Outputs

Upon successful completion, you should have:

1. **Formal PRD Document** containing:
   - Problem statement (user-focused, not solution-focused)
   - Success criteria (measurable outcomes)
   - Functional requirements (what it does)
   - Non-functional requirements (performance, security, integration)
   - User workflows (happy path and error cases)
   - Acceptance criteria (how to verify each requirement)
   - Out-of-scope items (what we're explicitly NOT doing)
   - Assumptions and constraints (from Grill Me)
   - Open questions (for implementation team to resolve)
   - Testing strategy (types of tests needed)

2. **Requirement Traceability**
   - Each requirement has a unique identifier (FR-1, NFR-1, etc.)
   - Each requirement maps to acceptance criteria
   - Each requirement is traceable through to implementation issues

3. **Implementation Guidance**
   - Which patterns from [Core Patterns](../patterns/) apply?
   - Which process phases are relevant?
   - Which playbooks should the implementation team reference?
   - Technical decisions that should remain flexible vs. fixed

4. **Cross-Functional Sign-Off**
   - Product manager approval
   - Technical lead review
   - (Optional) Security/compliance review if applicable

### Skill Verification Checklist

- [ ] Problem statement uses user language, not solution language
- [ ] Success criteria are measurable and testable
- [ ] Each functional requirement has acceptance criteria
- [ ] Each non-functional requirement has measurable targets
- [ ] User workflows are described from user perspective
- [ ] Workflows are platform/tool-agnostic (not prescribing how to build)
- [ ] Out-of-scope items are explicitly listed
- [ ] Constraints and dependencies are documented (from Grill Me)
- [ ] Open questions are listed with implications
- [ ] Testing strategy is described (unit/integration/E2E, acceptance criteria)
- [ ] PRD can be handed to implementation team without additional questions
- [ ] All stakeholders have explicitly approved the PRD
- [ ] No requirement is phrased as implementation (e.g., "use React" is hidden in acceptance criteria)

---

## Decision Flow

```
START: Clear Problem Statement (from Grill Me)
│
├─ Review problem statement
│  ├─ Clear and user-focused? → Continue
│  └─ Needs clarification? → Iterate Grill Me
│
├─ Define functional requirements
│  ├─ Core features clear? → Continue
│  ├─ Scope boundaries clear? → Continue
│  └─ Requirements testable? → Continue
│
├─ Define non-functional requirements
│  ├─ Performance targets set? → Continue
│  ├─ Security requirements clear? → Continue
│  └─ Integration requirements identified? → Continue
│
├─ Design user workflows
│  ├─ Happy path described? → Continue
│  ├─ Error paths defined? → Continue
│  └─ Workflows platform-agnostic? → Continue
│
├─ Identify acceptance criteria
│  ├─ Each requirement has criteria? → Continue
│  └─ Criteria are observable (not implementation)? → Continue
│
├─ Surface open questions
│  ├─ Ambiguities documented? → Continue
│  └─ Risks identified? → Continue
│
├─ Verify alignment
│  ├─ Product owner approves? → Continue
│  ├─ Tech lead approves? → Continue
│  └─ Stakeholders aligned? → Continue
│
└─ → READY FOR DECOMPOSITION PHASE (PRD to Issues)
```

---

## When NOT to Use This Skill

- **Trivial work:** Minor bug fixes or maintenance tasks may not warrant a full PRD
- **Urgent fire-fighting:** Production incidents may proceed with minimal specification
- **Work already specified:** If a detailed spec exists, skip to [PRD to Issues skill](prd-to-issues.md)
- **Exploratory work:** Spikes and prototypes may use lightweight specs instead

---

## Harness-Specific Implementation Notes

### For AI Agents

**Behavior:**
- Start by confirming the problem statement from Grill Me
- Work through each decision point systematically
- Ask clarifying questions if requirements are vague
- Synthesize responses into formal requirement language
- Feed back your understanding to stakeholder for verification

**Decision Rules:**
- If a requirement is vague: Ask for specific examples or measurable targets
- If a requirement sounds like implementation: Rephrase to describe behavior only
- If workflows are unclear: Ask user to walk through a specific scenario
- If acceptance criteria aren't testable: Rephrase to observable outcomes
- If stakeholder alignment is missing: Identify the disagreement and escalate

**Termination Criteria:**
- All checklist items are verified
- Stakeholder explicitly approves the PRD
- Agent should NOT continue refining once stakeholder approval is obtained (diminishing returns)

**Output Format:**
- Produce a well-formatted Markdown document (see template below)
- Include all required sections
- Use clear headings and structure for readability
- Provide as a shareable document or integration with repository

**Integration:**
- Store PRD in repository (e.g., `/docs/requirements/feature-name-prd.md`)
- Create a GitHub issue linking to the PRD
- Transition to [PRD to Issues skill](prd-to-issues.md) or notify user that implementation planning can begin

### For Humans Using This Skill

**Facilitation Approach:**
- Work with product owner, technical lead, and key stakeholders
- Use this skill as a structured template
- Iteratively build out each section
- Review and refine with team

**When to Involve AI:**
- Use AI to draft initial version based on Grill Me outputs
- Use AI to generate clarifying questions for vague requirements
- Use AI to synthesize acceptance criteria from requirements
- Use AI to identify missing requirements or gaps

**Review Process:**
1. Product owner reviews for business alignment
2. Technical lead reviews for feasibility
3. Implementation team reviews for clarity
4. Return to author for revisions if needed
5. Final approval before decomposition

---

## Relationship to Patterns

This skill operationalizes:
- **[Tracer Bullets](../patterns/tracer-bullets.md):** Defining the thin end-to-end requirement that will be implemented first
- **[Feedback Loops](../patterns/feedback-loops.md):** Iteratively refining requirements through stakeholder feedback
- **[Context Management](../patterns/context-management.md):** Capturing all context needed for implementation

---

## Relationship to Process

This skill is the **canonical implementation** of Process Phase 2 (Design). It formalizes the shared understanding from Grill Me into a specification that implementation teams can work from.

When Write PRD is complete, the process moves to:
- **Phase 3 (Decomposition):** Use the [PRD to Issues skill](prd-to-issues.md) to break the PRD into implementation tasks
- **Human Judgement Point:** Is the PRD detailed enough to decompose into issues?

---

## Skill Maturity Criteria

This skill is considered mature because:
- ✅ Canonical behavior is clearly defined (7 decision points, verification checklist)
- ✅ Success criteria are measurable (checklist items can be verified)
- ✅ It operationalizes a mature process phase (Phase 2)
- ✅ It's harness-agnostic (works for humans, AI agents, or hybrid teams)
- ✅ Output is tool-independent (Markdown, can be used with any issue tracker)
- ✅ Both humans and AI agents can execute and review it

---

## Common Pitfalls

| Pitfall | How to Recognize | How to Fix |
|---------|-------------------|-----------|
| **Prescribing implementation** | Requirements say "build with React" or "use this specific pattern" | Rephrase to describe behavior: "UI must load in <1 second" not "use React" |
| **Vague acceptance criteria** | Criteria say "should be fast" or "looks good" | Make measurable: "dashboard loads in <2 seconds (p95)" |
| **Skipping constraints** | Implementation discovers unforeseen dependencies | Review Grill Me output; add constraints section to PRD |
| **No stakeholder sign-off** | Multiple teams disagree about what was supposed to be built | Get explicit approval from product owner and tech lead before decomposition |
| **Scope creep** | PRD keeps growing; more requirements keep being added | Use "Out of Scope" section explicitly; be strict about saying "that's for v2" |
| **Incomplete workflows** | Implementation team discovers use cases no one mentioned | Walk through user scenarios with actual users/stakeholders |
| **No testing strategy** | PRD doesn't specify how to verify it works | Add "Testing Strategy" section describing unit/integration/E2E tests |
| **Over-specification** | PRD is 50+ pages; implementation team is overwhelmed | Trim to essentials; if it's complex, break into multiple smaller PRDs |

---

## PRD Template

```markdown
# Product Requirements Document: [Feature Name]

**Status:** [Draft / Ready for Review / Approved]  
**Owner:** [Product Manager Name]  
**Version:** 1.0  
**Last Updated:** [Date]

---

## Problem Statement

### User Problem
[One-sentence problem description]

### Impact
- **Who:** [User personas or roles]
- **Consequence:** [What happens if we don't solve this?]
- **Current Workaround:** [How do users handle it now?]

### Why Now?
[What changed that makes this urgent?]

---

## Success Criteria

**Primary Metric:** [How do we measure success?]  
**Secondary Metrics:** [Other indicators of success]  
**Non-Negotiable:** [Hard constraints that must be met]

---

## Functional Requirements

### Core Features (Must Have)

**FR-1: [Feature Name]**
- Description: [What does this feature do?]
- Input: [What goes in?]
- Output: [What comes out?]
- Acceptance Criteria:
  - [ ] [Specific, observable, testable behavior]
  - [ ] [Specific, observable, testable behavior]

### Nice-to-Have Features

**FR-X: [Feature Name]**
- Description: [Lower priority capability]
- Rationale: [Why is this lower priority?]

---

## Non-Functional Requirements

**Performance:**
- [Specific, measurable target] (e.g., page load time < 2 seconds)

**Security:**
- [Specific requirement] (e.g., data encrypted at rest)

**Reliability:**
- [Specific requirement] (e.g., 99.9% uptime)

**Compatibility:**
- [Platform/browser/version requirements]

---

## User Workflows

### Happy Path
[Describe step-by-step what a user does, what they see, what they expect]

### Error Cases
[Describe what happens when something goes wrong, how system recovers]

### Alternative Workflows
[Describe less common but valid user paths]

---

## Out of Scope

- [Explicitly NOT doing this]
- [Deferring this to a future release]
- [This is not our responsibility]

---

## Constraints and Dependencies

**Technical Constraints:**
- [What technical limitations do we operate within?]

**Business Constraints:**
- [Timeline, budget, organizational constraints]

**Dependencies:**
- [Other systems, teams, data sources this depends on]

---

## Open Questions

| Question | Implication | Owner |
|----------|-------------|-------|
| [Ambiguity] | [Why does this matter?] | [Who decides?] |

---

## Implementation Guidance

**Relevant Patterns:**
- [Which patterns apply?] - See [Pattern Name](../patterns/pattern-name.md)

**Relevant Playbooks:**
- [Which playbooks should implementation team reference?]

**Technical Decisions:**
- [Decisions that are fixed vs. flexible for implementation team]

---

## Testing Strategy

**Unit Tests:**
- [What unit behaviors need testing?]

**Integration Tests:**
- [What inter-system behaviors need testing?]

**End-to-End Tests:**
- [What user workflows need E2E validation?]

**Acceptance Criteria:**
- [How do we verify this is done?]

---

## Sign-Off

- [ ] Product Manager: [Name/Date]
- [ ] Technical Lead: [Name/Date]
- [ ] (Optional) Security Lead: [Name/Date]

```

---

## Examples

### Example 1: Feature PRD

**Problem Statement:** Users struggle to track items they want to purchase later without losing the information.

**Core Requirement:** Users must be able to save items for later and retrieve their wishlist across devices.

**Acceptance Criteria:**
- User can add any item to wishlist in < 2 taps
- Wishlist syncs to all user's devices within 5 seconds
- User can view wishlist offline (cached data)

### Example 2: Bug Fix PRD

**Problem Statement:** Dashboard loads too slowly on mobile devices, blocking user workflows.

**Core Requirement:** Dashboard must load in < 2 seconds (p95) on mobile networks.

**Acceptance Criteria:**
- Dashboard renders first content in < 1 second
- Interactive elements available in < 2 seconds
- Performance measured on 4G throttled connection

---

## Integration with Software Development Process

**When Write PRD Triggers:**
- Process Phase 2 (Design)
- Entry criteria: Clear problem statement from Grill Me

**Write PRD Outputs Become:**
- Inputs to Phase 3 (Decomposition) — specifically the [PRD to Issues skill](prd-to-issues.md)
- Canonical specification for implementation team

**Next Step After Write PRD:**
- If PRD is approved: Proceed to [PRD to Issues skill](prd-to-issues.md) (Phase 3)
- If PRD needs revision: Continue iterating Write PRD (stay in Phase 2)
- If PRD is too complex: Consider breaking into multiple smaller PRDs

---

## For AI Agents Implementing This Skill

### Decision Points to Automate

1. **Problem Confirmation:** "Is this problem statement clear and user-focused?"
2. **Requirement Completeness:** "Does this PRD include functional, non-functional, and acceptance criteria?"
3. **Testability:** "Can a tester verify each requirement without ambiguity?"
4. **Implementation-Independence:** "Are requirements phrased as behaviors, not implementations?"
5. **Stakeholder Alignment:** "Have all critical stakeholders reviewed and approved?"

### When to Ask Clarifying Questions

- Requirement is vague or measurable targets are missing
- Workflow is unclear or could be misinterpreted
- Acceptance criteria sound like implementation
- Open questions exist that materially affect implementation
- Stakeholder alignment is missing or conflicting

### When to Stop

Stop when:
- All checklist items are verified
- Product owner and technical lead approve
- No critical stakeholders have objections
- PRD can be handed to implementation team as-is

---

## Relationship to Other Skills

This skill builds on:
- **[Grill Me Skill](grill-me.md):** Takes discovery outputs and formalizes them

This skill feeds into:
- **[PRD to Issues Skill](prd-to-issues.md):** Takes formalized requirements and creates implementation issues

Together, these three skills form the **Discovery → Design → Decomposition** flow of the software development process.

---

