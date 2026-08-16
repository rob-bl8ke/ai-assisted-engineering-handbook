# Playbook: Create a PRD (Product Requirements Document)

## Overview

This playbook guides you through writing a clear, actionable Product Requirements Document (PRD) that communicates requirements to implementation teams (human or AI agents). A good PRD bridges the gap between business needs and engineering execution.

## When to Use This Playbook

- You have a feature idea and need to communicate it clearly to a development team
- You're clarifying vague requirements before implementation
- You're creating work for an AI agent to implement
- You're documenting requirements that span multiple issues or pull requests
- You're transitioning from discovery/design phase to implementation phase (see [Software Development Process, Phase 2](../processes/software-development.md#phase-2-design))

## Key Principles

- **One PRD, Many Issues:** A PRD describes the feature. Individual GitHub issues describe specific implementation tasks (see [Decompose Work Playbook](decompose-work.md))
- **Write for Your Audience:** PRDs should be understandable by both humans and AI agents
- **Include Context:** Why are we building this? What problem does it solve?
- **Be Specific:** Vague requirements lead to misaligned implementations
- **Separate Concerns:** Keep requirements separate from design decisions

---

## Phase 1: Clarify the Problem (15-20 minutes)

### 1.1 Define the Problem Statement

**Objective:** Clearly articulate the problem you're solving, not the solution.

**Steps:**
1. Write one sentence that starts with: "Users currently struggle to..."
2. Expand with: Why is this a problem? How does it affect users? What's the impact?
3. Ask: Is this a workflow problem? A capability gap? A quality issue?
4. Identify affected users or personas
5. Note the current workaround or pain point

**What to capture:**
```
## Problem Statement

**User Problem:** [One-sentence problem description]

**Impact:**
- Who is affected? [User personas or roles]
- What's the consequence of not solving this? [Business impact]
- What's the current workaround? [How users handle it now]

**Why Now?** [What changed that makes this urgent?]
```

**Example:**

```
## Problem Statement

**User Problem:** AI engineers spend excessive time switching context between their AI agent and their text editor when the agent encounters ambiguities about implementation approach.

**Impact:**
- Who: AI engineers using Claude Code, Copilot, or OpenCode
- Consequence: Context switching breaks flow; decisions get delayed; agents can't proceed without human input
- Current workaround: Manual back-and-forth messages, stopping agent, making decisions, resuming

**Why Now?** As agents handle more complex tasks, ambiguities increase. AFK (away from keyboard) workflows are blocked.
```

### 1.2 Identify Desired Outcome

**Objective:** Describe what success looks like (not how to achieve it).

**Steps:**
1. Complete: "After we build this, users will be able to..."
2. Describe the improved workflow or capability
3. Note measurable success criteria (if possible)
4. Distinguish between "must have" and "nice to have" outcomes

**What to capture:**
```
## Desired Outcome

**Success Statement:** After we build this, users will be able to...
[Describe capability in user terms, not technical terms]

**Measurable Success Criteria:**
- [ ] Users can [capability] in < [time/steps]
- [ ] [Metric] improves by [target]
- [ ] [User satisfaction indicator] reaches [target]

**Out of Scope (for this PRD):**
- [Things we're explicitly NOT doing]
- [Things we're deferring to later]
```

**Example:**

```
## Desired Outcome

**Success Statement:** After we build this, AI engineers can ask their agent to clarify ambiguities and receive guidance without stopping the agent or switching context.

**Measurable Success Criteria:**
- Agents can pause and ask for clarification via predefined decision points
- Human responds with decision (< 2 min response time in typical case)
- Agent resumes implementation without full restart
- Context is preserved across the pause

**Out of Scope:**
- Generic agent-human chat (out of scope for this release)
- Real-time streaming of agent thoughts (future enhancement)
```

---

## Phase 2: Define Requirements (30-45 minutes)

### 2.1 Functional Requirements

**Objective:** Describe what the system should do.

**Steps:**
1. List major features or capabilities
2. For each, describe inputs, processing, and outputs
3. Describe workflows or user journeys
4. Note any constraints or business rules
5. Distinguish between "must have" and "nice to have"

**What to capture:**
```
## Functional Requirements

### Core Requirements (Must Have)
- Req FR-1: [Clear description of capability]
  - Input: [What goes in?]
  - Processing: [What happens?]
  - Output: [What comes out?]
  - Acceptance criteria: [How do we know it works?]

### Nice-to-Have Requirements
- Req FR-X: [Lower priority capability]
  - Rationale: [Why is this lower priority?]
```

**Example:**

```
## Functional Requirements

### Core Requirements (Must Have)

**FR-1: Define Decision Point Markers**
- Input: Developer annotates code/process with decision markers
- Processing: System recognizes markers, captures context, creates decision checkpoint
- Output: Pause signal with context available to agent
- Acceptance: Agent receives structured decision point with full context

**FR-2: Agent Responds with Clarification Request**
- Input: Agent encounters situation matching decision point
- Processing: Agent formats clarification request (question, options, or needed information)
- Output: Developer receives focused question
- Acceptance: Developer receives exactly the information needed to decide (not a wall of text)

**FR-3: Developer Provides Decision**
- Input: Developer responds to clarification request
- Processing: Response is captured and validated
- Output: Decision is available to agent for next phase
- Acceptance: Agent can consume decision and proceed

### Nice-to-Have Requirements

**FR-4: Decision History**
- Rationale: Nice to see what decisions were made, but not critical for MVP
```

### 2.2 Non-Functional Requirements

**Objective:** Describe quality attributes (performance, reliability, security, etc.).

**Steps:**
1. Consider performance requirements: How fast must it be?
2. Consider reliability: How often can it fail?
3. Consider security: What data needs protection?
4. Consider scalability: How many users/requests?
5. Consider maintainability: How easy should it be to modify?

**What to capture:**
```
## Non-Functional Requirements

| Attribute | Requirement | Rationale |
|-----------|-------------|-----------|
| **Performance** | [e.g., Agent receives decision < 1 sec after developer submits] | [Why?] |
| **Reliability** | [e.g., No data loss of decision history] | [Why?] |
| **Security** | [e.g., Only authenticated users can make decisions] | [Why?] |
| **Usability** | [e.g., Decision request must fit in one screen] | [Why?] |
| **Maintainability** | [e.g., Decision logic should be in separate module] | [Why?] |
```

**Example:**

```
## Non-Functional Requirements

| Attribute | Requirement | Rationale |
|-----------|-------------|-----------|
| **Performance** | Decision available to agent within 1 second | Minimize agent pause time |
| **Reliability** | No decision data loss (all decisions persisted) | Maintain auditability and resumability |
| **Security** | Only the developer who started the session can decide | Prevent unauthorized decisions |
| **Usability** | Clarification request fits on one screen | Developers should see full request without scrolling |
| **Maintainability** | Decision logic in separate decision module | Make it easy to extend with new decision types |
```

### 2.3 Integration Requirements

**Objective:** Describe how this feature integrates with existing systems.

**Steps:**
1. What existing systems does this touch?
2. What APIs or interfaces does it use or provide?
3. What data flows between systems?
4. What dependencies are there?
5. What's the deployment model?

**What to capture:**
```
## Integration Requirements

### External Dependencies
- [ ] Does this require changes to [system X]?
- [ ] Does this require new APIs from [system Y]?
- [ ] Does this require database schema changes?
- [ ] Does this require infrastructure changes?

### Data Flows
[Diagram or description of data flowing between systems]

### Interfaces Provided
[Any new endpoints, libraries, or APIs this creates]
```

**Example:**

```
## Integration Requirements

### External Dependencies
- Requires new method on Agent interface: `async pauseForDecision(checkpoint)`
- Requires new table in database: `decision_checkpoints`
- Requires update to context manager (see Context Management pattern)

### Data Flows
Developer → Agent (pause request with checkpoint context)
Agent → Developer (formatted clarification request)
Developer → Agent (decision response)

### Interfaces Provided
- Agent method: `await agent.pauseForDecision(DecisionCheckpoint)`
- Developer method: `await developer.provideDecision(DecisionResponse)`
- Event: `agent.on('waitingForDecision', callback)`
```

---

## Phase 3: Define Scope and Constraints (15-20 minutes)

### 3.1 Scope Boundaries

**Objective:** Be explicit about what's included and excluded.

**Steps:**
1. List what IS in scope for this PRD/implementation
2. List what IS NOT in scope (even if related)
3. Note any phasing: What's in MVP vs. Phase 2 vs. future?
4. List any dependencies or blockers

**What to capture:**
```
## Scope

### In Scope (This Release)
- [Feature/capability]
- [Feature/capability]

### Out of Scope (For Now)
- [Related feature that's not included]
- [Enhancement that can be done later]

### Dependencies and Blockers
- Requires [something else] to be completed first
- Blocked by [issue or work]
```

**Example:**

```
## Scope

### In Scope (MVP)
- Decision point markers in code
- Agent pause-and-ask workflow
- Developer response collection
- Decision persists across agent pause

### Out of Scope (Phase 2)
- Decision history UI
- Automatic decision suggestion
- Multi-developer decision approval flow
- Integration with approval workflows

### Dependencies
- Requires [Context Management pattern](../patterns/context-management.md) implementation first
- Requires agent framework update (see issue #X)
```

### 3.2 Constraints and Assumptions

**Objective:** Document technical and business constraints.

**Steps:**
1. What constraints must we work within? (Technical, regulatory, business, timeline)
2. What are we assuming about the system, users, or environment?
3. What happens if assumptions are wrong?
4. How will we validate assumptions?

**What to capture:**
```
## Constraints and Assumptions

### Technical Constraints
- [e.g., Must work with existing agent framework without major refactoring]
- [e.g., Must not increase memory footprint by more than 10%]

### Business Constraints
- [e.g., Must be implementable in 2 weeks]
- [e.g., Must work on Windows and macOS]

### Assumptions
- [e.g., We assume developers can respond to decisions within 5 minutes]
- [e.g., We assume most decision points are yes/no questions]

### Risk if Assumptions Wrong
- [e.g., If assumption is wrong, we may need to support more complex decision types]
```

**Example:**

```
## Constraints and Assumptions

### Technical Constraints
- Must not modify agent execution model (pause, don't restart)
- Must work with existing database (no breaking schema changes)

### Business Constraints
- Must be implementable in 1 sprint
- Must work for single-developer workflows (collaboration deferred)

### Assumptions
- Developers will be available to respond to agent clarifications within 5 minutes
- Most clarifications are simple yes/no or multiple-choice decisions
- Developers won't need to write complex explanations

### Validation Plan
- If assumption 1 wrong: Add timeout/escalation for long pauses
- If assumption 2 wrong: Add free-text clarification support
- If assumption 3 wrong: Improve UI for text input
```

---

## Phase 4: Define User Experience (15-25 minutes)

### 4.1 User Workflows

**Objective:** Describe how users interact with this feature.

**Steps:**
1. Identify key user personas or use cases
2. For each, describe the workflow from start to finish
3. Include both happy path and error cases
4. Note decision points where users must take action
5. Use the [Feedback Loops Pattern](../patterns/feedback-loops.md) to identify where feedback is collected

**What to capture:**
```
## User Experience

### Workflow: [User Type] - [Workflow Name]

**Setup Phase:**
1. User does X
2. System responds with Y
3. User decides to proceed

**Main Workflow:**
1. User initiates [action]
2. System prompts for [decision point]
3. User provides [input]
4. System [processes and continues]
5. System delivers [result]

**Error Cases:**
- If [error condition], then [recovery]

**Feedback Loop:**
- At each step, system provides clear feedback
- User can undo or change decisions (if applicable)
```

**Example:**

```
## User Experience

### Workflow: AI Engineer - Ask Agent for Clarification

**Setup Phase:**
1. Engineer starts AFK agent to implement feature
2. Agent begins implementation
3. Agent encounters decision point (marked in requirements)

**Main Workflow:**
1. Agent pauses and formats clarification request
   - "I need to decide: Should validation happen on client or server?"
   - "Option A: Client (fast, less server load, less secure)"
   - "Option B: Server (slower, secure, single source of truth)"
2. Engineer sees the question with options
3. Engineer selects an option or writes response
4. System captures decision
5. Agent resumes with decision applied
6. Agent continues implementation

**Error Cases:**
- If engineer doesn't respond within 30 min: Send reminder notification
- If decision is ambiguous: Agent asks clarifying question
- If engineer changes mind: Can provide new decision (revokes previous)

**Feedback Loop:**
- Engineer sees agent status: "Waiting for decision on validation location"
- Engineer sees decision was accepted: "Resuming with server validation"
- At end, engineer sees all decisions made during session
```

### 4.2 Error Handling and Edge Cases

**Objective:** Describe what happens when things go wrong.

**Steps:**
1. What can go wrong in each phase?
2. How should the system handle each error?
3. What should the user see?
4. How can users recover?

**What to capture:**
```
## Error Handling

| Scenario | Cause | User Impact | Recovery |
|----------|-------|-------------|----------|
| [Error scenario] | [Root cause] | [What user sees] | [How to recover] |

### Specific Edge Cases
- [Edge case]: [How we handle it]
- [Edge case]: [How we handle it]
```

**Example:**

```
## Error Handling

| Scenario | Cause | User Impact | Recovery |
|----------|-------|-------------|----------|
| Agent times out waiting | Network issue or user offline | Agent shows "Waiting for decision..." status | Automatic retry when user returns; can also force resume |
| Invalid decision provided | Developer provides answer not in expected format | Agent shows error: "That option isn't recognized" | Agent re-asks question with valid options |
| Developer changes mind | User provides new decision before agent resumes | Old decision is replaced | Automatic, seamless; no user action needed |

### Specific Edge Cases
- **Offline developer:** If developer goes offline, agent can either wait or proceed with default decision (configurable)
- **Rapid decisions:** Multiple decisions needed quickly - all collected in sequence
- **Reverse decision:** Developer realizes previous decision was wrong - can override
```

---

## Phase 5: Technical Context and Implementation Guidance (15-20 minutes)

### 5.1 Relevant Patterns and Processes

**Objective:** Link this PRD to patterns and processes that guide implementation.

**Steps:**
1. Which patterns does implementation need to follow?
   - [Vertical Slicing Pattern](../patterns/vertical-slicing.md) for decomposition
   - [Tracer Bullets Pattern](../patterns/tracer-bullets.md) for minimal end-to-end implementation
   - [Context Management Pattern](../patterns/context-management.md) for information flow
   - [TDD Pattern](../patterns/test-driven-development.md) for test-driven development
   - [Feedback Loops Pattern](../patterns/feedback-loops.md) for gathering feedback
2. Which process phases apply? (See [Software Development Process](../processes/software-development.md))
3. Are there related decisions or previous implementations to learn from?

**What to capture:**
```
## Implementation Guidance

### Patterns to Apply
- **Vertical Slicing:** Slice this into independently testable, deployable units
  [Rationale: Makes it easier to get feedback early]
  
- **Tracer Bullets:** Build one end-to-end flow first
  [Rationale: Get working code quickly, learn what's missing]

- **TDD:** Write tests before implementation
  [Rationale: Test the decision checkpoint interface before implementing]

- **Context Management:** Be intentional about what context agent has at each step
  [Rationale: Prevent context overload when asking for decisions]

- **Feedback Loops:** After each slice, gather feedback before proceeding
  [Rationale: Discover missing requirements early]

### Process Alignment
- This PRD documents the end of Phase 2 (Design)
- Implementation will follow Phases 3-5 (Decomposition, Implementation, Validation)
- See [Software Development Process](../processes/software-development.md) for full process
```

**Example:**

```
## Implementation Guidance

### Patterns to Apply
- **Vertical Slicing:** First slice: add decision point markers and agent pause capability
  Second slice: add decision response collection and persistence
  Third slice: add decision history and UI

- **Tracer Bullets:** Get one end-to-end decision flow working (agent pause → developer decision → agent resume)
  Even with hardcoded decision types or minimal UI

- **TDD:** Start with test for `DecisionCheckpoint` data structure
  Then test `pauseForDecision()` method
  Then test agent integration

- **Context Management:** When agent asks for decision, include ONLY:
  - The question
  - Valid options
  - Relevant context (< 200 chars)
  - NOT full implementation details

### Process Alignment
- This is a Phase 2 (Design) deliverable
- Implementation proceeds via Phase 3-5 workflow
- See [Decompose Work Playbook](decompose-work.md) to create implementation tasks
```

### 5.2 Testing Strategy

**Objective:** Define how we'll verify this works.

**Steps:**
1. What must we test? (See [TDD Pattern](../patterns/test-driven-development.md))
2. What tests should we write? (Unit, integration, end-to-end)
3. What's the acceptance test for "done"?
4. How will we test error cases?

**What to capture:**
```
## Testing Strategy

### Test Categories

**Unit Tests:**
- DecisionCheckpoint creation and validation
- Decision response handling
- State transitions (waiting → decided → resumed)

**Integration Tests:**
- Agent pause-and-ask workflow
- Developer response collection
- State persistence across pause/resume

**End-to-End Tests:**
- Full workflow: marking decision point → agent pausing → developer deciding → agent resuming
- Error scenarios: timeout, invalid response, network failure

**Acceptance Criteria (How We Know It's Done):**
- [ ] All unit tests pass
- [ ] Integration tests pass
- [ ] End-to-end workflow works with real agent
- [ ] Developer can make a decision and agent resumes without losing context
- [ ] All error cases handled gracefully
```

**Example:**

```
## Testing Strategy

### Unit Tests
- `new DecisionCheckpoint()` creates valid checkpoint
- `parseDecision(response)` correctly parses developer response
- Invalid responses are rejected with clear error message
- Checkpoint can transition: created → waiting → decided → applied

### Integration Tests
- Agent.pauseForDecision() pauses execution and returns to waiting state
- DecisionHandler accepts developer response and updates checkpoint state
- Paused agent can resume with decision applied
- Checkpoint state is persisted and recoverable after restart

### End-to-End Tests
- Developer runs agent with AFK mode
- Agent encounters decision point during implementation
- Agent pauses and requests decision via UI
- Developer provides decision
- Agent resumes and continues implementation with decision applied

### Acceptance Criteria
- [ ] All tests pass (100% pass rate)
- [ ] Decision checkpoint survives agent pause/resume
- [ ] Developer context is preserved across pause
- [ ] Error recovery works (timeout, invalid input, etc.)
```

---

## Phase 6: Document Open Questions and Decisions (10 minutes)

### 6.1 Open Questions

**Objective:** Document what we're uncertain about and how we'll resolve it.

**Steps:**
1. List any questions or ambiguities
2. For each, note: Why is this unclear? When do we need to decide? How will we decide?
3. Identify which questions must be answered before implementation vs. later

**What to capture:**
```
## Open Questions and Decisions

### Must Resolve Before Implementation
- **Q1: [Question]**
  - Why unclear? [Context]
  - How to decide? [Resolution approach]
  - Deadline: [When do we need this decided?]
  - Owner: [Who will decide?]

### Can Resolve During Implementation
- **Q2: [Lower priority question]**
  - Why: [Context]
  - Approach: [How we'll discover the answer]
```

**Example:**

```
## Open Questions and Decisions

### Must Resolve Before Implementation
- **Q1: Should decision history be persisted permanently or only for the current session?**
  - Why unclear: Trade-off between auditability (permanent) and simplicity (session-only)
  - How to decide: Gather user feedback; check if audit requirements exist
  - Deadline: Before implementation starts
  - Owner: Product lead

### Can Resolve During Implementation
- **Q2: What should happen if agent times out waiting for decision?**
  - Why: Edge case behavior, depends on use case
  - Approach: Build with configurable timeout during implementation; learn from feedback
```

---

## Phase 7: Review and Finalize (10-15 minutes)

### 7.1 Review Checklist

**Before you consider this PRD complete:**

```
[ ] Problem statement is clear and doesn't assume a solution
[ ] Desired outcome is specific and measurable (if possible)
[ ] Functional requirements are detailed enough to implement
[ ] Non-functional requirements are explicit
[ ] Scope boundaries are clear (in vs. out)
[ ] Constraints and assumptions are documented
[ ] User workflows are described for both happy path and errors
[ ] Implementation guidance links to relevant patterns
[ ] Testing strategy is defined
[ ] Open questions are documented with resolution plans
[ ] PRD is reviewed by at least one other person
[ ] PRD is clear enough that an AI agent could implement it
```

### 7.2 PRD Structure

**Ensure your PRD includes these sections:**

```markdown
# [Feature Name] - PRD

## Problem Statement
[Clear articulation of problem, not solution]

## Desired Outcome
[What success looks like]

## Functional Requirements
[What the system should do]

## Non-Functional Requirements
[Quality attributes]

## Integration Requirements
[How it connects to existing systems]

## Scope
[What's in vs. out]

## Constraints and Assumptions
[Limits and unproven beliefs]

## User Experience
[Workflows and interactions]

## Error Handling
[What happens when things go wrong]

## Implementation Guidance
[Which patterns and processes apply]

## Testing Strategy
[How we'll verify it works]

## Open Questions
[Uncertainties and how we'll resolve them]

## Appendix
[Diagrams, additional context, references]
```

---

## For AI Agents

### What This Playbook Tells You

A well-written PRD:
1. **Separates problem from solution** - You'll know WHAT to build, and THEN you decide HOW
2. **Provides clear acceptance criteria** - You'll know when implementation is done
3. **Links to patterns** - You'll know which architectural patterns to apply
4. **Includes context** - You'll understand why this matters

### How to Implement from a PRD

1. Read the problem statement to understand context
2. Review functional requirements to understand the feature
3. Check integration requirements to understand dependencies
4. Use the implementation guidance section to choose patterns
5. Apply [Vertical Slicing Pattern](../patterns/vertical-slicing.md) to decompose the work
6. Create implementation tasks (see [Decompose Work Playbook](decompose-work.md))

### If Requirements Are Unclear

- Flag unclear requirements before starting implementation
- Use [Context Management Pattern](../patterns/context-management.md) to ask focused questions
- Don't guess or over-interpret—ask for clarification
- Update the PRD with the clarification for future reference

---

## Integration with Software Development Process

This playbook supports **Phase 2 (Design)** of the [Software Development Process](../processes/software-development.md).

It produces the formal requirements document that becomes the input for:
- **Phase 3 (Decomposition):** See [Decompose Work Playbook](decompose-work.md) to break the PRD into issues
- **Phase 4 (Implementation):** Developers implement to PRD requirements

---

## Common Pitfalls

| Pitfall | What Goes Wrong | Solution |
|---------|-----------------|----------|
| **Solution in Problem** | PRD describes how to solve, not what to solve | Rewrite problem statement to focus on user need, not implementation |
| **Too Vague** | Readers interpret requirements differently | Add specific examples and acceptance criteria for each requirement |
| **Too Prescriptive** | Developers can't make design choices | Define WHAT users need, not HOW to build it; leave design choices open |
| **Missing Context** | Readers don't understand why this matters | Add problem statement and desired outcome sections |
| **Unrealistic Scope** | PRD describes too much for one release | Prioritize: what's MVP vs. Phase 2? Move nice-to-haves out of scope |
| **No Acceptance Criteria** | Developers finish but requirements aren't clearly met | Add specific, testable acceptance criteria for each requirement |
| **Assumes Technical Stack** | PRD is tied to specific database, framework, etc. | Define requirements in terms of behavior, not technology |

---

## Related Patterns and Playbooks

- **Pattern:** [Context Management](../patterns/context-management.md) — Keep requirements focused and clear
- **Pattern:** [Vertical Slicing](../patterns/vertical-slicing.md) — Decompose PRD into sliceable units
- **Pattern:** [Feedback Loops](../patterns/feedback-loops.md) — Gather feedback on PRD before implementation
- **Process:** [Software Development Process - Phase 2 (Design)](../processes/software-development.md#phase-2-design) — Where this playbook fits
- **Playbook:** [Decompose Work](decompose-work.md) — Next step: turn PRD into implementation issues

---

## References

- PRD Template and Examples in [Software Development Process](../processes/software-development.md)
- [Context Management Pattern](../patterns/context-management.md) for writing clear requirements
- [Vertical Slicing Pattern](../patterns/vertical-slicing.md) for thinking about decomposable features
- [Feedback Loops Pattern](../patterns/feedback-loops.md) for validating requirements with users
