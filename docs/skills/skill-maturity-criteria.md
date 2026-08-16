# Skill Maturity Criteria

**When should a playbook become a skill? When should a skill become an agent?**

This document provides criteria for identifying when a playbook is mature enough to be operationalized as a skill, and when a skill is ready for full automation as an agent.

---

## What Makes a Playbook Mature Enough to Become a Skill?

A playbook should be considered for skill-ification when it meets the following criteria:

### 1. Clear, Repeatable Workflow

**Criterion:** The workflow has a consistent structure that can be decomposed into distinct phases or decision points.

**Questions:**
- Can the workflow be described as a series of 4-8 decision points?
- Does the workflow produce consistent, predictable outputs?
- Can multiple teams/individuals follow the same playbook and get comparable results?

**Evidence:**
- Playbook has clear phase structure (e.g., "Phase 1: Understand, Phase 2: Design, Phase 3: Verify")
- Decision points are explicit (e.g., "If X, do Y; if Z, do W")
- Output checklist is clear and verifiable

**Example:**
- [Grill Me Skill](grill-me.md): Clear 5 decision points → Mature for skill-ification ✓
- [Repository Exploration Playbook](../playbooks/repository-exploration.md): 7 phases with clear structure → Candidate for Repository Explorer skill

### 2. Harness-Agnostic Core Behavior

**Criterion:** The playbook describes what to do, not how to do it with specific tools.

**Questions:**
- Can the playbook be executed by humans without any software tools?
- Can the playbook be executed by AI without tool-specific configuration?
- Are tool-specific details separated from canonical workflow?

**Evidence:**
- Playbook doesn't prescribe "use GitHub Issues" — it says "create tracked work items"
- Playbook doesn't prescribe "use AI agent" — it says "ask clarifying questions"
- Playbook focuses on behavior and outcomes, not specific tools

**Example:**
- ✓ [Write PRD Skill](write-prd.md): Says "create formal specification with acceptance criteria" (tool-agnostic)
- ✗ Hypothetical playbook: "Use Claude to draft the PRD in Notion" (tool-specific) → Not ready for skill-ification

### 3. Automatable Decision Points

**Criterion:** Key decisions in the workflow can be automated or elevated to clear human judgment points.

**Questions:**
- Can each decision point be described as "if condition X, then Y"?
- Are there clear criteria for which path to take?
- Can an AI system ask questions and evaluate responses?

**Evidence:**
- Decision flow can be rendered as a flowchart
- Each decision has clear criteria for yes/no or A/B/C branching
- Judgment points are explicitly marked (e.g., "🟡 Human Judgement Point")

**Example:**
- ✓ [PRD to Issues Skill](prd-to-issues.md): Can identify vertical slices programmatically based on criteria
- ✗ Hypothetical playbook with subjective language: "Choose the design that feels right" → Requires human judgment, not automatable

### 4. Measurable Success Criteria

**Criterion:** It's clear when the workflow is complete and successful.

**Questions:**
- Is there a verification checklist?
- Can someone other than the author verify completion?
- Are success criteria binary (done/not done) or graduated?

**Evidence:**
- Playbook includes "Skill Verification Checklist"
- Each checklist item is specific and testable
- Checklist items are phrased as observable outcomes (not subjective)

**Example:**
- ✓ [Grill Me Skill](grill-me.md): Verification checklist with 8 items; all checkable
- ✗ Hypothetical playbook: "Understand the problem well" → Subjective, not verifiable

### 5. Value from Automation

**Criterion:** Automating this playbook saves significant time or improves outcomes.

**Questions:**
- How much time does the playbook currently take (human execution)?
- How much time would automation save?
- Are there other benefits (consistency, fewer errors, parallel execution)?
- Is the benefit proportional to the implementation effort?

**Evidence:**
- Playbook is regularly executed (not a rare edge case)
- Manual execution is time-consuming or error-prone
- Multiple teams would benefit from automation
- Automation enables new capabilities (AFK workflows, parallelization)

**Example:**
- ✓ [Grill Me Skill](grill-me.md): Used on every feature; saves time on discovery iteration; enables AFK agents to explore ideas
- ? Hypothetical playbook: "Organizing team retrospectives" → Valuable but lower ROI for automation

### 6. Operational Maturity

**Criterion:** The playbook has been proven in practice across multiple contexts.

**Questions:**
- Has the playbook been used successfully multiple times?
- Have edge cases been identified and addressed?
- Have pitfalls been documented?
- Is the playbook stable or frequently changing?

**Evidence:**
- Playbook has been used in 3+ real projects
- Common pitfalls section is substantial
- Playbook has remained stable for 2+ releases
- Multiple teams have used it successfully

**Example:**
- ✓ [Create PRD Playbook](../playbooks/create-prd.md): Based on established practices; proven across multiple feature types
- ✗ Hypothetical new playbook: "Experimental process" → Needs more validation

### 7. Clear Harness Boundaries

**Criterion:** The skill definition can be separated from tool-specific implementation.

**Questions:**
- Can the canonical skill be described without mentioning specific tools?
- Can harness-specific notes be isolated to a separate section?
- Would different harnesses (Claude, Copilot, OpenCode) have different implementations but same canonical behavior?

**Evidence:**
- Skill has "Canonical Skill Behavior" section independent of tools
- Skill has "Harness-Specific Implementation Notes" that doesn't change canonical behavior
- Canonical behavior could be implemented by humans, Claude, Copilot, or OpenCode

**Example:**
- ✓ [PRD to Issues Skill](prd-to-issues.md): Canonical behavior is tool-agnostic; GitHub-specific implementation is in harness notes
- ✗ Skill that says "Call Claude API with prompt X" → Harness-specific detail leaking into canonical behavior

---

## Checklist: Is This Playbook Ready to Become a Skill?

```
Criterion 1: Clear, Repeatable Workflow
- [ ] Workflow has 4-8 distinct decision points or phases
- [ ] Multiple teams can follow playbook and get similar results
- [ ] Output structure is consistent

Criterion 2: Harness-Agnostic Core
- [ ] Playbook works without specific software tools
- [ ] Playbook works with humans OR AI agents (not tool-specific)
- [ ] Tool/platform specifics are separated from core workflow

Criterion 3: Automatable Decision Points
- [ ] Each decision can be described as "if X, then Y"
- [ ] Clear criteria exist for which path to take
- [ ] An AI system could reasonably implement these decisions

Criterion 4: Measurable Success Criteria
- [ ] Verification checklist exists
- [ ] All checklist items are specific and testable
- [ ] Someone other than author can verify completion

Criterion 5: Value from Automation
- [ ] Playbook is regularly used (not rare edge case)
- [ ] Manual execution is time-consuming or error-prone
- [ ] Automation saves significant time or improves outcomes
- [ ] Multiple teams would benefit

Criterion 6: Operational Maturity
- [ ] Playbook has been used successfully 3+ times
- [ ] Common pitfalls are documented
- [ ] Playbook is stable (not frequently changing)
- [ ] Proven across multiple contexts/teams

Criterion 7: Clear Harness Boundaries
- [ ] Canonical behavior is tool-agnostic
- [ ] Harness-specific details can be isolated
- [ ] Could be implemented by humans or multiple AI systems

DECISION:
- [ ] All 7 criteria met → Ready to become a skill
- [ ] 5-6 criteria met → Close; consider addressing gaps
- [ ] < 5 criteria met → Keep as playbook; revisit later
```

---

## Decision: Playbook to Skill

**If all 7 criteria are met, the playbook is ready to become a skill.**

When converting a playbook to a skill:

1. **Extract canonical behavior** from procedural details
   - Keep: Decision points, success criteria, relationship to process/patterns
   - Remove: Step-by-step instructions (move to "For Humans Using This Skill")

2. **Create explicit decision flow**
   - Render as flowchart or decision tree
   - Ensure all branches are covered
   - Label judgment points vs. automated branches

3. **Write harness-specific sections**
   - "For AI Agents" — How to implement autonomously
   - "For Humans" — How to facilitate or execute manually
   - Keep canonical behavior unchanged

4. **Define termination criteria**
   - Verification checklist must be explicit
   - Termination conditions must be unambiguous
   - "When to stop" must be clear to prevent infinite loops

5. **Link to process and patterns**
   - Which process phase does this implement?
   - Which patterns does it operationalize?
   - What skills does it feed into?

**Examples of playbooks that have become skills:**
- [Grill Me Skill](grill-me.md) ← [Repository Exploration Playbook's](../playbooks/repository-exploration.md) discovery phase
- [Write PRD Skill](write-prd.md) ← [Create PRD Playbook](../playbooks/create-prd.md)
- [PRD to Issues Skill](prd-to-issues.md) ← [Decompose Work Playbook](../playbooks/decompose-work.md)

---

## Decision: Skill to Agent

**When should a mature skill become a fully automated agent?**

A skill is ready for agent-ification when:

1. **Canonical behavior is stable** — The workflow is proven and not changing
2. **Harness implementation is proven** — At least one harness (Claude, Copilot, etc.) has successfully automated the skill
3. **Integration is clear** — The agent has defined inputs, outputs, and integration points
4. **Judgment points are clarified** — Human escalation paths are clear
5. **Performance is acceptable** — The agent can execute the skill reliably

### Skill-to-Agent Maturity Checklist

```
- [ ] Skill has been operational for 2+ releases
- [ ] At least one harness has successfully automated the skill
- [ ] Integration with downstream systems (GitHub Issues, documentation) is proven
- [ ] Edge cases and error conditions are documented
- [ ] Human escalation paths are clear and tested
- [ ] Performance metrics show the agent is faster/better than manual execution
- [ ] Automated tests verify the agent behavior
- [ ] The agent's outputs require minimal human post-processing
```

**Examples:**
- **[PRD to Issues Skill](prd-to-issues.md)** → **Issue Decomposition Agent**
  - Skill creates GitHub issues with structure; agent automates issue creation and dependency linking
  - Human judgment: Accept/reject slice proposal; resolve ambiguities

- **[Grill Me Skill](grill-me.md)** → **Discovery Agent**
  - Skill asks clarifying questions; agent conducts discovery conversation with stakeholder
  - Human judgment: Confirm understanding; resolve conflicts

---

## Relationship to Process Evolution

As playbooks mature into skills and skills mature into agents, the process evolves:

```
Manual Process
    ↓
Documented Playbook
    ↓
Structured Skill
    ↓
Partially Automated Agent
    ↓
Fully Automated Workflow
```

The handbook captures knowledge at each level. Documentation remains valuable even when automation exists — it explains *why* the process works, not just *how*.

---

## Current Skill Maturity Status

| Knowledge | Status | Next Step |
|-----------|--------|-----------|
| [Grill Me Skill](grill-me.md) | Mature skill | Ready for agent implementation |
| [Write PRD Skill](write-prd.md) | Mature skill | Ready for agent implementation |
| [PRD to Issues Skill](prd-to-issues.md) | Mature skill | Ready for agent implementation |
| [Repository Exploration Playbook](../playbooks/repository-exploration.md) | Mature playbook | Candidate for Repository Explorer skill |
| [Review Implementation Playbook](../playbooks/review-implementation.md) | Mature playbook | Candidate for Code Reviewer skill |
| [Investigate Bug Playbook](../playbooks/investigate-bug.md) | Mature playbook | Candidate for Bug Investigator skill |

---

## Common Mistakes

| Mistake | Why It's a Problem | How to Avoid |
|---------|-------------------|--------------|
| **Rushing to agent before skill maturity** | Agent fails or behaves unpredictably; humans lose trust | Follow all 7 criteria before agent implementation |
| **Automating inherently subjective workflows** | Automation can't capture nuance; over-specifying misses edge cases | Ensure clear, objective decision criteria before automation |
| **Losing documentation when automating** | Documentation remains valuable for understanding *why*; humans need it for troubleshooting | Keep canonical behavior document even after agent implementation |
| **Overfitting to one tool** | Skill becomes tool-dependent; can't be ported to other harnesses | Separate canonical behavior from tool-specific implementation |
| **Unclear termination criteria** | Agent runs forever or stops too early | Explicit "when to stop" is non-negotiable for automation |
| **No human escalation path** | Agent can't handle edge cases; workflow breaks | Define judgment points and escalation criteria |

---

## See Also

- [Skills Overview](README.md) — Navigation to all skills
- [Software Development Process](../processes/software-development.md) — The processes that skills operationalize
- [Playbooks](../playbooks/) — Detailed procedural guidance
- [Patterns](../patterns/) — Reusable techniques skills apply

---

