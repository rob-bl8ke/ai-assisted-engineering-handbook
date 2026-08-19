# Skills

**Skills** are executable or semi-executable AI instructions implementing a specific capability. They operationalize mature processes and playbooks, making complex workflows accessible to both humans and AI agents.

A skill is more formal than a playbook and more automated than a process. It defines the **canonical behavior and decision points** needed to accomplish a goal, leaving tool-specific implementation to harness-specific adapters.

---

## Quick Navigation

### Initial Skills (MVP)

These three skills form the core workflow for AI-assisted software engineering:

1. **[Grill Me](grill-me.md)** — Establish shared understanding
   - Operationalizes: [Process Phase 1.2 - Shared Understanding](../processes/software-development.md#12-shared-understanding-grill-me)
   - Detailed guidance: [Repository Exploration Playbook](../playbooks/repository-exploration.md)
   - When to use: When you have a vague idea and need to explore it with stakeholders
   - Canonical behavior: Build a design tree, ask one question at a time, discover facts directly, ask the user for decisions, and confirm shared understanding before acting

2. **[Write PRD / To Spec](write-prd.md)** — Create formal specifications
   - Operationalizes: [Process Phase 2 - Design](../processes/software-development.md#phase-2-design)
   - Detailed guidance: [Create PRD Playbook](../playbooks/create-prd.md)
   - When to use: When you have a clear problem and need to write a formal specification
   - Canonical behavior: Transform confirmed discovery output into a formal specification with behavior, success criteria, implementation decisions, testing decisions, and tracker publication when appropriate

3. **[PRD to Issues](prd-to-issues.md)** — Decompose into implementation issues
   - Operationalizes: [Process Phase 3 - Decomposition](../processes/software-development.md#phase-3-decomposition)
   - Detailed guidance: [Decompose Work Playbook](../playbooks/decompose-work.md)
   - When to use: When you have an approved PRD and need to break it into implementable issues
   - Canonical behavior: Create vertical slices, define GitHub issues, set up dependencies, document decomposition strategy

### Engineering Extensions

1. **[Grill With Docs](grill-with-docs.md)** — Establish shared understanding while maintaining domain docs
   - Builds on: [Grill Me](grill-me.md)
   - Uses: [Domain Docs](../agents/domain.md)
   - When to use: When discovery involves domain language, bounded contexts, or durable architecture decisions
   - Canonical behavior: Grill the plan, use DDD-style ubiquitous language, update `CONTEXT.md` when terms crystallize, and create ADRs only for durable trade-off decisions

---

## Skill Workflow

These three skills work together to move work from vague idea to implementation-ready issues:

```
Idea / Problem Statement
        ↓
   Grill Me Skill
   (Discovery Phase)
        ↓
Shared Understanding
        ↓
  Write PRD Skill
   (Design Phase)
        ↓
 Formal Specification
        ↓
PRD to Issues Skill
(Decomposition Phase)
        ↓
GitHub Issues Ready for Implementation
```

---

## Skill Maturity Criteria

Not every playbook should become a skill. [See Skill Maturity Criteria](skill-maturity-criteria.md) for guidance on:

- When should a playbook be operationalized as a skill?
- What makes a skill "mature" enough for automation?
- How do skills evolve into agents?

---

## Using Skills

### For Humans

Each skill is designed to be:
- Readable as structured guidance (decision trees, checklists)
- Followable without specialized tools
- Facilitatable with a team or stakeholder group
- Verifiable with a checklist of outcomes

**Workflow:**
1. Read the skill overview to understand its purpose
2. Follow the decision flow or checklist
3. Use the templates and examples for reference
4. Verify the skill checklist to know when you're done
5. Proceed to the next skill in the workflow

### For AI Agents

Each skill is designed to be:
- Executable without modification (canonical behavior is tool-agnostic)
- Integrable with external tools (issue trackers, documentation systems)
- Verifiable (checklist provides clear termination criteria)
- Loopable (can iterate if verification fails)

**Workflow:**
1. Parse skill definition to extract decision points
2. Implement each decision point as a branching question or analysis step
3. Generate outputs according to skill specification
4. Verify against checklist
5. Integrate with downstream systems (GitHub issues, documentation)
6. Transition to next skill or report completion

**Implementation Notes:**
- Skills are harness-neutral; create harness-specific wrappers as adapters
- Termination criteria in each skill's checklist prevent infinite loops
- Decision points should be automated where possible; escalate to humans where needed
- Skill outputs should be shareable and version-controlled (Markdown, GitHub Issues, etc.)

### For Hybrid Teams

Skills enable human and AI collaboration:
- Humans handle judgment calls; AI handles data collection and synthesis
- Humans review AI outputs; AI implements human decisions
- Skills define clear handoff points between human and AI

Example: Grill Me Skill
- AI: Ask clarifying questions, synthesize answers
- Human: Verify understanding, make judgment calls, resolve conflicts
- AI: Document outputs, present for approval

---

## Skill Documentation Structure

Each skill follows this structure:

1. **Overview** - What the skill does, when to use it
2. **Canonical Skill Behavior** - Core decision points and outputs
3. **Decision Flow** - Visual flowchart of decision points
4. **When NOT to Use** - Boundary conditions
5. **Harness-Specific Notes** - Separated tool/platform-specific guidance
6. **Relationship to Patterns** - Which patterns does this skill operationalize?
7. **Relationship to Process** - Which process phase does this implement?
8. **Skill Maturity Criteria** - Why this skill is mature enough for automation
9. **Common Pitfalls** - Table of common mistakes and fixes
10. **Examples** - Real-world or realistic examples
11. **Integration Guidance** - How to integrate with next phases

This structure ensures:
- **Harness-agnostic core:** Canonical behavior works with any tool
- **Tool-specific adapters:** Platform differences are isolated
- **Clear boundaries:** When to use, when not to use
- **Automation-ready:** Decision points and checklists guide AI implementation
- **Human-readable:** Structures like decision trees and checklists for human users

---

## Skill vs. Playbook vs. Process vs. Pattern

| Concept | Purpose | Scope | Audience |
|---------|---------|-------|----------|
| **Pattern** | Reusable technique that can appear in many processes | Single principle (e.g., vertical slicing) | Architects, process designers |
| **Playbook** | Detailed procedural guidance ("how to do it") | Complete end-to-end activity | Any team member |
| **Skill** | Executable specification with decision points | Automatable workflow | Humans and AI agents |
| **Process** | End-to-end workflow with phases and decision points | Full project lifecycle | Project teams |
| **Scenario** | Entry points based on developer's situation | Specific common situation | Developers with a specific need |

**Relationships:**
```
Process
  ├─ Links to Playbooks (detailed how-to)
  │   └─ Some Playbooks mature into Skills
  │
  └─ Links to Patterns (reusable techniques)
      └─ Skills operationalize multiple patterns
```

---

## Hierarchy of Knowledge

From abstract to concrete:

```
Principle
    ↓
  Pattern (reusable technique)
    ↓
 Playbook (procedural guidance)
    ↓
   Skill (executable specification)
    ↓
  Agent (fully automated workflow)
```

**Example:**
```
Context Economy
       ↓
Repository Exploration (before implementation)
       ↓
Repository Exploration Playbook
       ↓
Repository Explorer Skill
       ↓
Repository Explorer Agent
```

---

## Future Skills (Planned)

Based on the PRD, these skills may be developed:

- **[Repository Explorer](../playbooks/repository-exploration.md)** — Systematically understand unfamiliar codebases
- **[Code Reviewer](../playbooks/review-implementation.md)** — Conduct systematic code reviews prioritizing bugs and risks
- **[Bug Investigator](../playbooks/investigate-bug.md)** — Find and fix bugs with root cause analysis

These will follow the same maturity criteria and harness-neutral design as the initial skills.

---

## Skill Maintenance

Use [Sync Skills With Upstream Repositories](../playbooks/sync-skills-with-upstream.md) when a skill depends on external repository knowledge and needs a scheduled PR-based refresh process.

### When to Create a New Skill

- Playbook is well-established and widely used
- Workflow is highly structured with clear decision points
- Automation would add significant value
- Criteria from [Skill Maturity Criteria](skill-maturity-criteria.md) are met

### When to Update a Skill

- User feedback reveals ambiguities in decision points
- New tool integrations require adapter examples
- Process changes require workflow adjustments
- Effectiveness metrics show the skill can be simplified

### When to Deprecate a Skill

- More effective skill replaces it
- Workflow is no longer needed
- Tool ecosystem change makes skill obsolete

---

## Integrations

### GitHub Issues Integration

Skills output GitHub issues with:
- Titles following format: `[ACTION] [WHAT]`
- Descriptions referencing skill and PRD
- Acceptance criteria from skill verification checklists
- Blocked-by relationships for dependency tracking

### Documentation Integration

Skills reference:
- Process phases (why this skill exists)
- Playbooks (detailed how-to guidance)
- Patterns (reusable techniques)
- Examples (realistic scenarios)

### AI Agent Integration

Skills define:
- Canonical behavior (harness-agnostic)
- Decision points (what to ask or analyze)
- Termination criteria (when to stop)
- Output format (what to produce)

Tool-specific adapters implement:
- API calls (GitHub, Jira, Claude, Copilot)
- UI interactions (if interactive)
- Integration handoffs

---

## Contributing

To add or update skills:

1. Start with a well-established playbook
2. Check [Skill Maturity Criteria](skill-maturity-criteria.md)
3. Extract decision points and automate them
4. Separate canonical behavior from harness-specific notes
5. Define clear termination criteria (verification checklist)
6. Add examples showing human and AI execution
7. Create harness-specific adapters as needed
8. Test with humans and AI agents
9. Update this README with navigation entry

---

## See Also

- [Software Development Process](../processes/software-development.md) — The overarching workflow these skills operationalize
- [Playbooks](../playbooks/) — Detailed procedural guidance for each skill
- [Patterns](../patterns/) — Reusable techniques these skills apply
- [Skill Maturity Criteria](skill-maturity-criteria.md) — When to create a skill from a playbook

---

