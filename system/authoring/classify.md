# Classification Guide

**Purpose:** How to classify new knowledge into appropriate handbook categories  
**Audience:** AI agents integrating knowledge; humans proposing contributions  
**Related:** [README.md](README.md) (overview), [integrate.md](integrate.md) (after classification)

---

## Knowledge Categories

The handbook uses nine knowledge categories:

1. **Principle** — Underlying reasoning
2. **Pattern** — Reusable technique
3. **Process** — End-to-end workflow
4. **Playbook** — Step-by-step procedure
5. **Skill** — Executable AI capability
6. **Scenario** — Situational entry point
7. **Adapter** — Harness-specific guidance
8. **Example** — Platform-specific implementation
9. **Reference** — Provenance/source material

---

## Category Definitions & Decision Tree

### 1. Principle

**Definition:** Underlying reasoning that explains why practices exist

**Questions to ask:**
- Does this explain a *core idea* or *fundamental concept*?
- Is this reasoning that applies to many different situations?
- Does this answer "Why?" rather than "How?"

**Examples:**
- "Separate canonical knowledge from tool implementations" → Principle
- "Humans remain in control of important decisions" → Principle
- "Minimize token usage by focusing context" → Principle
- "Fail fast; escalate blockers immediately" → Principle

**Where it lives:** `docs/principles/[name].md`

**Characteristics:**
- Short and focused (1-3 pages typically)
- Explains the reasoning
- References patterns and processes that embody it
- Language-independent, tool-independent

**When to create:**
- Only when the principle is foundational to multiple patterns/processes
- Not for every idea (most ideas are patterns, not principles)

---

### 2. Pattern

**Definition:** Reusable technique or approach that appears in multiple contexts

**Questions to ask:**
- Is this a *repeatable technique* that can be applied in different ways?
- Would multiple processes or playbooks benefit from knowing about this?
- Is this a *how-to* at a meta level?
- Can this be implemented with different tools or platforms?

**Examples:**
- Ralph Loop (autonomous feedback loop) → Pattern
- Test-Driven Development (red-green-refactor cycle) → Pattern
- Tracer Bullets (build minimal end-to-end path first) → Pattern
- Vertical Slicing (break features into independent slices) → Pattern

**Where it lives:** `docs/patterns/[name]/README.md` (with examples and adapters)

**Characteristics:**
- Core concept explained without tool/platform assumptions
- Can be implemented multiple ways
- Has examples in different platforms
- Often has adapters for different AI tools
- References processes and playbooks that use it

**When to create:**
- When the technique is reusable across multiple contexts
- When it's mature enough to document
- When multiple playbooks or processes refer to it

**Structure:**
```
docs/patterns/[name]/
├── README.md (core concept)
├── examples/
│   ├── bash.md
│   └── powershell.md
└── adapters/
    ├── claude.md
    ├── copilot.md
    └── opencode.md
```

---

### 3. Process

**Definition:** End-to-end workflow for a major engineering activity

**Questions to ask:**
- Does this describe a *complete sequence of phases or steps*?
- Does it answer "What sequence should I follow?"
- Is this a workflow that applies across multiple scenarios?
- Does it have alternative paths or rainy paths?

**Examples:**
- Software Development Process (from idea to delivery) → Process
- Feature Development (planning through shipping) → Process
- Bug Investigation (reproduce through root cause analysis) → Process
- AFK Implementation (autonomous agent execution workflow) → Process

**Where it lives:** `docs/processes/[name].md`

**Characteristics:**
- Describes phases or major steps
- Includes rainy paths (failure and recovery)
- Explains when to use each phase
- References patterns and playbooks used in each phase
- May have decision points and alternatives

**When to create:**
- When describing an end-to-end workflow
- When multiple phases are sequenced together
- When the workflow applies across multiple situations

**Not a process:**
- Single techniques (those are patterns)
- Specific step-by-step procedures (those are playbooks)
- Tool-specific workflows (tools use adapters to consume processes)

---

### 4. Playbook

**Definition:** Step-by-step procedure for performing a specific activity

**Questions to ask:**
- Does this provide *detailed how-to guidance*?
- Can someone follow it step-by-step?
- Is this answering "How do I do this specific task?"
- Does it have verification criteria or success metrics?

**Examples:**
- "How to explore an unfamiliar repository" → Playbook
- "How to write a PRD" → Playbook
- "How to review implementation" → Playbook
- "How to investigate a bug" → Playbook

**Where it lives:** `docs/playbooks/[name].md`

**Characteristics:**
- Numbered steps or clear sequence
- Specific and actionable
- References relevant patterns
- Includes verification/success criteria
- Documents common mistakes

**When to create:**
- When providing detailed how-to guidance
- When the guidance is specific to a single activity
- When someone could follow it step-by-step

**Difference from Pattern:**
- Pattern: "Here's a technique that appears in many contexts"
- Playbook: "Here's how to perform this specific task"

---

### 5. Skill

**Definition:** Executable AI capability implementing a pattern or process

**Questions to ask:**
- Can this be *implemented as AI automation*?
- Does this operationalize a pattern or process?
- Does this have clear decision points?
- Can both humans and AI agents follow it?

**Examples:**
- Grill Me (discovery skill) → Skill
- Write PRD (specification writing) → Skill
- PRD to Issues (decomposition) → Skill
- Code Reviewer (code review) → Skill

**Where it lives:** `docs/skills/[name].md`

**Characteristics:**
- Implements a mature pattern or process
- Has explicit decision points
- Can be executed by AI agent or human
- Has verification checklist
- References the pattern/process it operationalizes
- May have harness-specific implementations

**When to create:**
- When a playbook or pattern is mature enough to automate
- When you've used it successfully multiple times
- When the procedure is well-defined

---

### 6. Scenario

**Definition:** Entry point based on a developer's situation

**Questions to ask:**
- Does this address a *specific developer situation*?
- Does this answer "What should I do given my current situation?"
- Is this a high-level problem framing?
- Would this help a reader find the right process/pattern?

**Examples:**
- "I have a vague feature idea" → Scenario
- "I need to fix a production bug" → Scenario
- "I inherited an unfamiliar codebase" → Scenario
- "I want to try unattended agent execution" → Scenario

**Where it lives:** `docs/scenarios/[name].md`

**Characteristics:**
- Describes a situation, not a procedure
- Links to relevant processes, patterns, playbooks
- Short (mostly navigation)
- May have decision tree
- Helps reader find what they need

**When to create:**
- When you want to organize processes/patterns by situation
- When readers might not know what to search for

**Note:** Scenarios should primarily link to existing processes/patterns rather than duplicate them.

---

### 7. Adapter

**Definition:** Harness-specific guidance (Claude, GitHub Copilot, OpenCode)

**Questions to ask:**
- Is this guidance *specific to one AI harness*?
- Does this explain "How do I use this pattern with [Tool]?"
- Would this differ significantly if using a different AI tool?
- Is this tool-agnostic enough to be in the core?

**Examples:**
- "Ralph Loop with Claude API" → Adapter
- "Context management in GitHub Copilot Chat" → Adapter
- "OpenCode autonomous execution" → Adapter
- "Copilot-specific prompt patterns" → Adapter

**Where it lives:** `docs/patterns/[name]/adapters/[harness].md`

**Characteristics:**
- Assumes the tool is Claude, Copilot, or OpenCode
- References core concept as prerequisite
- Tool-specific API calls, configuration, or behavior
- Separate from canonical concept
- May include examples in code

**When to create:**
- When tool-specific guidance would make core concept unclear
- When multiple tools need the pattern
- When the tool has unique capabilities or limitations

**NOT adapters:**
- Core concept explanations (those go in pattern README)
- Platform-specific code (those are examples)
- General guidance (should go in core)

---

### 8. Example

**Definition:** Platform-specific implementation (Bash, PowerShell, Python, JavaScript, etc.)

**Questions to ask:**
- Is this guidance *specific to one programming language or shell*?
- Would this code look significantly different in another platform?
- Does this explain "How do I implement this in [Platform]?"
- Is the algorithm identical across platforms?

**Examples:**
- "Ralph Loop implementation in Bash" → Example
- "Ralph Loop implementation in PowerShell" → Example
- "Context management in Python" → Example
- "Deploying to Windows vs. macOS" → Example

**Where it lives:** `docs/patterns/[name]/examples/[platform].md`

**Characteristics:**
- Platform/language-specific
- References core pattern/process as basis
- Complete, working code
- Separate from canonical concept
- May include setup and configuration

**When to create:**
- When implementing concept in a specific platform
- When platform-specific differences matter
- When multiple platforms support the concept

**NOT examples:**
- Core algorithm (that's in the pattern)
- Tool-specific guidance (that's an adapter)
- General guidance (should go in core)

---

### 9. Reference

**Definition:** Provenance; source material where an idea originated

**Questions to ask:**
- Does this *cite or reference external source material*?
- Is this a blog post, article, video, or conversation?
- Does this record "Where did this idea come from?"
- Would understanding the source help readers?

**Examples:**
- Matt Pocock's Ralph Loop blog post → Reference
- Conference talk on tracer bullets → Reference
- Practitioner experience with TDD → Reference
- Academic paper on context windows → Reference

**Where it lives:** `docs/references/[name].md`

**Characteristics:**
- Records source and link
- Brief summary of the source
- Why it's relevant to the handbook
- Linked from handbook documents that use the idea
- May include quotes or key concepts

**When to create:**
- When integrating external knowledge
- When a process or pattern originated elsewhere
- When attribution is important

---

## Classification Process: Step-by-Step

### Step 1: Read and Understand

Fully understand what you're classifying before proceeding.

### Step 2: Ask the Questions

For each knowledge category, ask the defining questions:

```
Is this a PRINCIPLE?
  └─ Does it explain why something works?
      └─ Is it foundational to multiple patterns/processes?
          └─ YES? Principle

Is this a PATTERN?
  └─ Is it a repeatable technique?
      └─ Can it be applied in multiple contexts?
          └─ YES? Pattern

Is this a PROCESS?
  └─ Is it an end-to-end workflow?
      └─ Does it have multiple phases/steps?
          └─ YES? Process

Is this a PLAYBOOK?
  └─ Is it step-by-step how-to guidance?
      └─ Specific to one activity?
          └─ YES? Playbook

Is this a SKILL?
  └─ Can this be automated?
      └─ Operationalizes a pattern/process?
          └─ YES? Skill

Is this a SCENARIO?
  └─ Addresses a specific situation?
      └─ Primarily navigation to other resources?
          └─ YES? Scenario

Is this ADAPTER?
  └─ Specific to Claude/Copilot/OpenCode?
      └─ Explains how a pattern works with that tool?
          └─ YES? Adapter

Is this an EXAMPLE?
  └─ Specific to Bash/PowerShell/Python/etc.?
      └─ Platform-specific implementation?
          └─ YES? Example

Is this a REFERENCE?
  └─ Cites external source material?
      └─ Records provenance?
          └─ YES? Reference
```

### Step 3: Record Multiple Classifications

Most knowledge will have **multiple** classifications.

**Example 1:**
```
Input: "Here's how to implement Ralph Loop with Claude in Bash"

Classification:
├─ Pattern: Ralph Loop
├─ Adapter: Claude
└─ Example: Bash
```

**Example 2:**
```
Input: "A practitioner's experience with TDD in Python"

Classification:
├─ Pattern: Test-Driven Development
├─ Example: Python
└─ Reference: Practitioner blog post
```

**Example 3:**
```
Input: "We should always keep humans in control of important decisions"

Classification:
├─ Principle: Human-in-the-loop
└─ Reference: Our team's experience
```

---

## Classification Examples

### Example 1: Blog Post on Ralph Loops

**Input:** A blog post explaining how to run Claude Code with a Bash script that iteratively implements features using TDD

**Analysis:**
- Is this a principle? NO (it's more specific)
- Is this a pattern? YES (Ralph Loop is a repeatable technique)
- Is this a process? NO (Ralph Loop is a technique, not an end-to-end workflow)
- Is this a playbook? NO (too general; doesn't give step-by-step procedure)
- Is this a skill? MAYBE (could be implemented as an automated skill)
- Is this a scenario? NO
- Is this an adapter? YES (Claude-specific)
- Is this an example? YES (Bash-specific)
- Is this a reference? YES (external source material)

**Classification:** Pattern + Adapter (Claude) + Example (Bash) + Reference

**Action:** 
- Update `docs/patterns/ralph-loop/README.md` (core concept)
- Add/update `docs/patterns/ralph-loop/adapters/claude.md`
- Add/update `docs/patterns/ralph-loop/examples/bash.md`
- Add reference entry with link to blog post

---

### Example 2: New Development Workflow

**Input:** A team shares their workflow: "Before we implement, we do discovery, write a PRD, decompose into issues, and then implement in a Ralph Loop with senior review"

**Analysis:**
- Is this a principle? NO
- Is this a pattern? MAYBE (the Ralph Loop part, but the overall workflow is bigger)
- Is this a process? YES (end-to-end workflow)
- Is this a playbook? NO (too high-level)
- Is this a skill? MAYBE (parts are automatable)
- Is this a scenario? NO
- Is this an adapter? NO
- Is this an example? NO
- Is this a reference? YES (team experience)

**Classification:** Process + Reference

**Action:**
- Check if this already exists in `docs/processes/software-development.md`
- If similar, update existing process with their insights
- Add to reference section noting the team's contribution
- Ensure Ralph Loop is referenced in the AFK implementation phase

---

### Example 3: Tool-Specific Configuration

**Input:** "When using OpenCode, you can set the temperature parameter to 0.1 for more conservative code generation"

**Analysis:**
- Is this a principle? NO
- Is this a pattern? NO (too specific to one tool)
- Is this a process? NO
- Is this a playbook? NO
- Is this a skill? NO
- Is this a scenario? NO
- Is this an adapter? YES (OpenCode-specific configuration)
- Is this an example? NO (not platform-specific)
- Is this a reference? NO (or minimal reference)

**Classification:** Adapter (OpenCode)

**Action:**
- Find or create `docs/patterns/[relevant-pattern]/adapters/opencode.md`
- Add OpenCode temperature configuration guidance
- Link from core pattern if applicable

---

### Example 4: Implementation Comparison

**Input:** "Here's how context management works in PowerShell vs. Bash"

**Analysis:**
- Is this a principle? NO
- Is this a pattern? YES (Context Management pattern)
- Is this a process? NO
- Is this a playbook? NO
- Is this a skill? NO
- Is this a scenario? NO
- Is this an adapter? NO (platform, not tool)
- Is this an example? YES (platform-specific implementations)
- Is this a reference? NO (or minimal)

**Classification:** Pattern + Example (PowerShell and Bash)

**Action:**
- Update `docs/patterns/context-management/README.md` (core concept, if needed)
- Add/update `docs/patterns/context-management/examples/bash.md`
- Add/update `docs/patterns/context-management/examples/powershell.md`
- Cross-reference both from core concept

---

## Tips for Accurate Classification

1. **Read multiple times.** Understand before classifying.

2. **Ask the core question.** Each category answers a specific question:
   - Principle: "Why does this work?"
   - Pattern: "What is this repeatable technique?"
   - Process: "What's the complete workflow?"
   - Playbook: "How do I do this step-by-step?"
   - Skill: "How can this be automated?"
   - Scenario: "What situation is this for?"
   - Adapter: "How is this done with [Tool]?"
   - Example: "How is this done on [Platform]?"
   - Reference: "Where did this come from?"

3. **Look for multiple categories.** Most knowledge will have 2-4 categories.

4. **Err on the side of principle/pattern over playbook.** If something seems both:
   - Principle vs. Pattern: Principle is more fundamental, appears in many contexts
   - Pattern vs. Playbook: Pattern is more general, playbook is step-by-step
   - Playbook vs. Skill: Playbook is always documentation; skill is automatable

5. **Tool/platform is usually an addon, not primary.** A thing is a Pattern, and you also note it has Claude and PowerShell guidance.

6. **When in doubt, ask:**
   - Can this be implemented multiple ways? → Pattern
   - Is this specific to one tool/platform? → Adapter/Example
   - Is this original research or restatement? → Reference

---

## See Also

- **[README.md](README.md)** — Authoring guide overview
- **[integrate.md](integrate.md)** — What to do after classification
- **[validate.md](validate.md)** — Validation after integration
- **[../../AGENTS.md](../../AGENTS.md)** — Quick AI agent reference

