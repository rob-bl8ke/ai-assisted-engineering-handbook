# Skill: Grill Me

## Overview

**Grill Me** is an interactive discovery skill that establishes shared understanding between stakeholders, product owners, and development teams (human or AI) by systematically exploring and clarifying requirements before design begins.

**Canonical Purpose:** Transform a vague idea, feature request, or problem statement into a clearly understood problem with documented constraints, assumptions, and stakeholder alignment.

**Related Process Phase:** [Software Development Process, Phase 1.2: Shared Understanding](../processes/software-development.md#12-shared-understanding-grill-me)

**Detailed How-To:** [Repository Exploration Playbook](../playbooks/repository-exploration.md) (for understanding existing context) and Phase 1.2 of the process

---

## Canonical Skill Behavior

### Entry Conditions

You have:
- An initial idea, feature request, or problem statement
- Access to stakeholders, product owners, or domain experts (human or via context)
- Context about the existing codebase or system (if applicable)

### Core Decision Points

**Decision 1: Understand the Current State**
- Is this about understanding an existing codebase, fixing a bug, or adding new functionality?
- **If exploring existing code:** Use repository exploration patterns to gather context first
- **If about new functionality:** Proceed to Requirement Discovery

**Decision 2: Identify the Core Problem (Not the Solution)**
- Does the requester describe the problem or jump to solutions?
- **If proposing a solution:** Ask "What problem does this solve?"
- **If describing a problem:** Document it and ask clarifying questions

**Decision 3: Explore Hidden Constraints**
- What business constraints exist? (timeline, budget, regulatory)
- What technical constraints exist? (compatibility, performance, security)
- What dependencies exist? (other teams, systems, data)
- **Surface each constraint and verify understanding**

**Decision 4: Identify Stakeholders and Their Concerns**
- Who needs to agree on this work?
- What does each stakeholder care about? (performance, user experience, maintainability)
- Are there conflicting perspectives that need resolution?
- **Document stakeholder positions and any conflicts**

**Decision 5: Assess Clarity Level**
- Can the development team now explain this work without asking more questions?
- Are all technical ambiguities resolved?
- Are success criteria measurable and agreed-upon?
- **If gaps remain:** Return to earlier decision points
- **If sufficiently clear:** Proceed to design phase (Create PRD skill)

### Skill Outputs

Upon successful completion, you should have:

1. **Clear Problem Statement**
   - One-sentence summary of what users struggle with
   - Why this matters (impact on users/business)
   - Why now (urgency factors)

2. **Documented Constraints**
   - Business constraints (timeline, budget, regulatory, organizational)
   - Technical constraints (platform limitations, compatibility, performance targets)
   - Dependencies (other systems, teams, data sources)

3. **Stakeholder Map**
   - List of stakeholders who must agree
   - Each stakeholder's primary concerns
   - Any conflicting perspectives that need resolution

4. **Success Criteria (Draft)**
   - How will we know this is done?
   - What are measurable indicators?
   - What are non-negotiable requirements?

5. **Questions for Next Phase**
   - Open questions that the design phase (PRD) should address
   - Areas flagged for further investigation
   - Risks or dependencies that need mitigation

### Skill Verification Checklist

- [ ] Problem is described in user terms, not solution terms
- [ ] At least 3 clarifying questions were asked and answered
- [ ] At least one hidden constraint was uncovered
- [ ] Stakeholder concerns have been documented
- [ ] Success criteria are described (even if draft)
- [ ] Development team could explain this to someone else without clarifying further
- [ ] No active disagreements remain unresolved
- [ ] Context about existing codebase/system is captured (if applicable)

---

## Decision Flow

```
START: Vague Idea / Problem Statement
│
├─ Understand current state
│  ├─ Exploring existing codebase? → Use Repository Exploration patterns
│  └─ Adding new functionality? → Continue
│
├─ Identify core problem (not solution)
│  ├─ Is this a solution proposal? → Ask "What problem does this solve?"
│  └─ Clear problem stated? → Document it
│
├─ Explore constraints
│  ├─ Business constraints documented? → Continue
│  ├─ Technical constraints identified? → Continue
│  └─ Dependencies mapped? → Continue
│
├─ Identify stakeholders and concerns
│  ├─ All stakeholders listed? → Continue
│  ├─ Each stakeholder's concerns known? → Continue
│  └─ Conflicts surfaced? → Document and resolve
│
├─ Assess clarity
│  ├─ Can team explain without more questions? → Continue
│  ├─ All technical ambiguities resolved? → Continue
│  └─ Success criteria agreed? → Continue
│
└─ → READY FOR DESIGN PHASE (Create PRD)
   OR → ITERATE (return to earlier decision points if gaps remain)
```

---

## When NOT to Use This Skill

- **Trivial, well-defined work:** Skip to lightweight work items if the problem is already crystal clear
- **Urgent fire-fighting:** Minimal grilling for production incidents; focus on immediate stabilization
- **Pure refactoring with no behavioral change:** May have minimal discovery needs
- **Maintenance or minor bug fixes:** May proceed directly to Create PRD or implementation

---

## Harness-Specific Implementation Notes

### For AI Agents

**Behavior:**
- Present questions one at a time in a conversational manner
- Acknowledge each answer and build context iteratively
- Ask follow-up questions based on responses
- Synthesize and feed back your understanding to verify accuracy

**Decision Rules:**
- If a response is vague, ask: "Can you give me a specific example?"
- If a constraint is mentioned, ask: "Are there exceptions to this constraint?"
- If a stakeholder perspective emerges, ask: "Are there other people who might disagree?"
- If success criteria aren't measurable, ask: "How would we measure success?"

**Termination Criteria:**
- User/stakeholder confirms that clarity is sufficient
- Checklist items are verified
- User explicitly says "let's move to design phase"
- Agent should NOT continue adding new questions once clarity threshold is met (diminishing returns)

**Integration:**
- Store outputs in a shareable format (document, conversation summary, or structured record)
- Transition to [Write PRD skill](write-prd.md) or notify user that design phase can begin
- Flag any unresolved conflicts for human escalation

### For Humans Using This Skill

**Facilitation Approach:**
- Host a discussion or meeting with stakeholders and technical team
- Use this skill as a facilitation guide
- Document answers as the conversation happens
- Circle back to any unresolved disagreements before proceeding

**When to Involve AI:**
- Use AI as a scribe to document conversations
- Use AI to generate clarifying questions on topics requiring deeper exploration
- Use AI to synthesize answers into organized problem statement
- Use AI to identify patterns or missing information

---

## Relationship to Patterns

This skill operationalizes:
- **[Context Management](../patterns/context-management.md):** Systematically building and verifying context
- **[Feedback Loops](../patterns/feedback-loops.md):** Iteratively refining understanding through questions and answers
- **[Tracer Bullets](../patterns/tracer-bullets.md):** Validating assumptions early before full design

---

## Relationship to Process

This skill is the **canonical implementation** of Process Phase 1.2 (Shared Understanding). It automates the discovery conversation within the broader software development workflow.

When Grill Me is complete, the process moves to:
- **Phase 2 (Design):** Use the [Write PRD skill](write-prd.md) to formalize the understanding into requirements
- **Human Judgement Point:** Does the understanding seem sufficient to move forward?

---

## Skill Maturity Criteria

This skill is considered mature because:
- ✅ Canonical behavior is clearly defined (decision points, verification checklist)
- ✅ Core workflow is simple and repeatable (5 decision points)
- ✅ Success criteria are measurable (checklist items can be verified)
- ✅ It operationalizes a mature process phase (Phase 1.2)
- ✅ It's harness-agnostic (works for humans, AI agents, or hybrid teams)
- ✅ Both humans and AI agents can execute it without tool-specific training

---

## Common Pitfalls

| Pitfall | How to Recognize | How to Fix |
|---------|-------------------|-----------|
| **Jumping to solutions** | Stakeholder keeps proposing features instead of explaining problems | Repeatedly ask "What problem would this solve?" Redirect to problem space, not solution space |
| **Unclear constraints** | Team discovers new constraints during implementation that weren't mentioned | Explicitly ask about business, technical, and dependency constraints separately |
| **Unresolved conflicts** | Stakeholders disagree about requirements or priorities | Surface conflict explicitly. Escalate to decision-maker. Document the decision and why. |
| **Over-discovering** | Skill never ends; more questions keep emerging | Use the checklist as termination criteria. "Are we now ready to write requirements?" If yes, stop. |
| **Insufficient context** | Design phase reveals major gaps in understanding | For work touching existing code, use Repository Exploration patterns first |
| **Skipping grilling** | Proceeding to PRD without discovery; design goes sideways | Grill phase saves time. Incomplete discovery leads to rework later |

---

## Examples

### Example 1: Feature Request (E-Commerce)

**Initial Request:** "We need a wishlist feature for our store."

**Grill Me Execution:**

1. **Problem (Not Solution):** 
   - "What problem are users experiencing?" 
   - "Some customers want to save items for later without committing to purchase."
   - Problem: Users can't track items of interest across sessions

2. **Constraints:**
   - Business: Must launch within 2 sprints; mobile-first
   - Technical: Wishlist data must sync across devices; must work offline
   - Dependencies: Requires user authentication (already exists); requires cart system updates

3. **Stakeholders:**
   - Product: Wants to increase repeat purchases
   - Customer Support: Wants to reduce "where's my saved item?" inquiries
   - Mobile Team: Concerned about offline sync complexity
   - Security: Wants to ensure user data privacy

4. **Success Criteria:**
   - Users can save/remove items in < 2 taps
   - Wishlist syncs across devices within 5 seconds
   - Mobile app works offline for saved items
   - Customer support tickets about saved items drop 30%

**Output:** Ready for Design Phase. Proceed to Create PRD.

---

### Example 2: Bug Fix (Performance)

**Initial Report:** "The dashboard is slow on mobile."

**Grill Me Execution:**

1. **Problem Clarification:**
   - "How slow? When does it happen?"
   - "Dashboard loads in 8+ seconds; users see blank screen"
   - Problem: Dashboard load time blocks user workflows on mobile

2. **Constraints:**
   - Business: Mobile users are 40% of traffic; slow experience drives churn
   - Technical: Dashboard fetches 50+ fields; API calls run in series
   - Dependencies: API team maintains data endpoint; can't redesign schema

3. **Stakeholders:**
   - Product: Concerned about mobile churn
   - Mobile Team: Has hypotheses about N+1 query patterns
   - Backend Team: Can't change API schema; willing to add new endpoints

4. **Success Criteria:**
   - Dashboard loads in < 2 seconds (90th percentile)
   - Mobile and desktop load times are similar

**Output:** Ready for Design Phase. Proceed to Create PRD.

---

## Integration with Software Development Process

**When Grill Me Triggers:**
- Process Phase 1.2 (Shared Understanding)
- Entry criteria: Initial idea, feature request, or problem statement with gaps

**Grill Me Outputs Become:**
- Inputs to Phase 2 (Design) — specifically the [Write PRD skill](write-prd.md)
- Discovery documentation that informs technical decisions

**Next Step After Grill Me:**
- If understanding is sufficient: Proceed to [Write PRD skill](write-prd.md) (Phase 2)
- If understanding has gaps: Continue iterating Grill Me (stay in Phase 1.2)
- If work is too trivial to warrant a full PRD: Jump to Phase 3 (Decomposition)

---

## For AI Agents Implementing This Skill

### Questions to Ask

1. **Problem Understanding:**
   - "Can you describe the problem you're trying to solve?"
   - "Who experiences this problem and how does it affect them?"
   - "Why is this a priority now?"

2. **Current State:**
   - "How do users currently handle this?"
   - "What's broken or inefficient about the current approach?"
   - "What have you already tried?"

3. **Constraints:**
   - "Are there any hard deadlines or timeline constraints?"
   - "Are there technical or platform limitations we need to work within?"
   - "Do we need to integrate with any external systems?"

4. **Stakeholders:**
   - "Who needs to approve or agree on this work?"
   - "What does each stakeholder care most about?"
   - "Are there any conflicting priorities?"

5. **Success:**
   - "How will we know this is done?"
   - "What would success look like?"
   - "Are there any metrics that matter?"

### How to Handle Responses

- **Vague Answers:** Ask for specific examples
- **Solution-Focused Answers:** Redirect to problem space ("What problem does this solve?")
- **Incomplete Answers:** Note the gap and continue; come back to it
- **Conflicting Answers:** Document both perspectives; note that conflict needs resolution

### When to Stop

Stop when:
- The verification checklist is complete
- User confirms understanding is sufficient
- You can explain the problem to someone else without further clarification
- No active conflicts remain unresolved

---

## Relationship to Other Skills

This skill feeds into:
- **[Write PRD Skill](write-prd.md):** Takes Grill Me outputs and formalizes them into detailed requirements
- **[PRD to Issues Skill](prd-to-issues.md):** Uses formalized requirements to create implementation issues

Together, these three skills form the **Discovery → Design → Decomposition** flow of the software development process.

---

