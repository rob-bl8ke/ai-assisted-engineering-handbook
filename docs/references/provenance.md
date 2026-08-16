# Recording Provenance: Source Attribution Guidance

This guide explains how to document where knowledge in the handbook originated.

## Why Provenance Matters

Provenance answers the question: **Where did this idea come from?**

Recording provenance:
- Gives proper credit to originators
- Allows readers to inspect source material
- Helps assess confidence in different approaches  
- Enables tracking how ideas have evolved
- Respects intellectual property rights
- Shows that techniques come from proven practice

## What Requires Provenance

Add provenance documentation to:

1. **Canonical Patterns** — Core reusable techniques (e.g., Ralph Loop, Vertical Slicing, TDD)
2. **Processes** — End-to-end workflows (e.g., Feature Development, Bug Fixing)
3. **Principles** — Underlying reasoning (e.g., Context Economy, Human-in-the-Loop)
4. **Major Playbooks** — Complex procedures (e.g., Repository Exploration, Code Review)
5. **Published Skills** — Documented capabilities (e.g., Write PRD, Grill Me)

Less critical (but still helpful):

- Platform/Harness-specific examples (document the generic source instead)
- Adapters (reference the canonical pattern source instead)
- Cross-links and navigation (inherit from source concepts)

## Source Types

When recording provenance, specify the source type:

| Type | Example | What to Include |
|------|---------|-----------------|
| **Person** | Matt Pocock, Paul Graham | Name, possibly LinkedIn/GitHub profile |
| **Article** | "How to use AI for coding" blog post | Title, author, URL, publication date |
| **Video** | Talk at conference, YouTube video | Title, speaker, source URL, publication date |
| **Transcript** | Conference talk transcript, podcast transcript | Title, speaker, URL to transcript |
| **Documentation** | Claude Code documentation, GitHub Copilot guide | Product name, section reference, URL |
| **Experiment** | Internal testing of a technique | Experiment name, what was tested, results |
| **Tool/Repository** | Open-source tool, reference implementation | Tool name, author, GitHub URL |
| **Book** | Published book on software engineering | Title, author, ISBN, chapter reference |
| **Other** | Conversations, overheard techniques, etc. | Describe source as fully as possible |

## Format: Sources Section

Include a **Sources and Provenance** section in each document that requires it.

### Option 1: Simple Table Format (Recommended for MVP)

Use this format for clarity and scanability:

```markdown
## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Ralph Loop Algorithm | Matt Pocock | Person / Practitioner | Demonstrated effective iterative development cycle | N/A |
| Bash Implementation | Claude API Testing | Experiment | Validated script-based workflow | N/A |
| PowerShell Equivalence | Internal Porting | Experiment | Confirmed cross-platform compatibility | N/A |
| Claude Integration | Claude Documentation | Documentation | API patterns and best practices | https://docs.anthropic.com |
```

### Option 2: Narrative Format (For Complex Origins)

Use this format when sources are tightly intertwined:

```markdown
## Origins and Evolution

The Ralph Loop pattern originated from **Matt Pocock's** demonstration of iterative AI-assisted 
development in Claude Code. The core concept of maintaining context and repeatedly refining solutions 
through a feedback cycle drew inspiration from **traditional test-driven development** practices 
and **agile iteration** principles.

The pattern was validated through **internal experiments** running identical Bash and PowerShell 
scripts to verify cross-platform portability. Claude-specific guidance was developed by consulting 
**Claude API documentation** and testing patterns within the Claude environment.

Additional refinements came from **community practitioner feedback** and **comparative testing** 
with GitHub Copilot and OpenCode, leading to the harness-neutral formulation documented here.
```

### Option 3: Detailed Attribution (For Mature Concepts)

Use this format when sources need more context:

```markdown
## Sources and Attribution

### Core Concept: Ralph Loop (6-step Iteration Cycle)
- **Primary Source:** Matt Pocock, AI-assisted development practitioner
- **Type:** Person / Practitioner Experience
- **Context:** Demonstrated effective feedback-loop-based iterative development in Claude Code
- **Date Encountered:** 2024
- **Evidence Level:** Widely adopted in community, multiple practitioners have validated independently

### Principle: Blocker Detection
- **Source:** Internal implementation experience and testing
- **Type:** Experiment
- **Context:** Experimentation showed that detecting specific error categories (compilation, test failure, 
  requirement ambiguity, timeout) enables effective loop termination decisions
- **Date Validated:** 2024
- **Note:** Similar concepts appear in continuous integration/deployment literature

### Implementation: Bash Version
- **Source:** Internal porting and validation
- **Type:** Experiment
- **Context:** Script-based Ralph Loop implementation created to demonstrate cross-platform portability
- **Date Created:** 2024
- **Platform:** macOS/Linux with Bash shell
```

## Provenance for Different Content Types

### For Patterns
Identify the originator and key sources:

```markdown
## Sources and Provenance

| Element | Source | Context |
|---------|--------|---------|
| Overall Pattern | [Originating person/publication] | [How did you learn about this?] |
| Core Algorithm | [If from published source] | [Specific chapter/section if applicable] |
| Validation | [How has this been proven in practice?] | [Your experience, others' experience, academic research] |
| Variations | [Sources of alternative approaches] | [Different ways to apply this pattern] |
```

### For Processes
Document the workflow lineage:

```markdown
## Sources and Provenance

This process is based on **[Original Source]** and has been adapted through:
- [Practitioner 1]'s guidance on [specific aspect]
- [Publication]'s research on [methodology]
- Internal experimentation with [specific scenarios]
- Community feedback from [practice or community]

Original workflow: [Link or reference to original source]
Key adaptations: [What changed and why]
```

### For Skills
Credit both originator and maintainers:

```markdown
## Provenance

**Original Concept:** [Person/Publication]
- [Context of original idea]

**Implementation:** [Your organization]
- [How you adapted it]
- [For what purpose]

**Refinements:** [Community/Practitioners]
- [What has improved over time]
```

### For Principles
Document philosophical origins:

```markdown
## Philosophical Origins

This principle builds on:
- **[Researcher/Author]** — [Specific work or contribution]
- **[Established Practice]** — [Industry standard or long-standing approach]
- **[Practitioner Experience]** — [How this has been validated in practice]
```

## Recording Experimental Validation

When you validate existing concepts through your own work:

```markdown
## Validation and Evolution

### Cross-Platform Testing (2024)
- **Experiment:** Ported Bash Ralph Loop script to PowerShell
- **Result:** Confirmed identical algorithm works across platforms with language-specific syntax changes
- **Significance:** Validates Ralph Loop pattern is truly platform-agnostic, not Bash-specific
- **Documentation:** See [PowerShell Example](../examples/powershell.md)

### Harness Comparison (2024)
- **Experiment:** Tested Ralph Loop implementation in Claude, Copilot, and OpenCode
- **Result:** All three harnesses support the core pattern; differences are in tool integration, not algorithm
- **Significance:** Proves Ralph Loop is harness-neutral
- **Documentation:** See [Adapter Comparison](adapters/) 
```

## Handling Uncertain or Multiple Origins

### When Origin Is Unclear
```markdown
## Provenance

**Historical Note:** This technique is widely used in software engineering communities, 
though its original source is not definitively known. It may have emerged independently 
in multiple communities. Known popularizers include:

- [Practitioner A]
- [Practitioner B]  
- [Academic Research C]

If you know the original source, please contribute it.
```

### When Multiple Sources Contributed
```markdown
## Sources and Provenance

This pattern synthesizes insights from multiple sources:

1. **Pattern Core:** From [Source A]
2. **Implementation Approach:** Inspired by [Source B]  
3. **Error Handling:** Based on [Source C]'s findings
4. **Validation:** Confirmed through [Internal Experiment D]

See [References](../references/README.md) for cross-linking guidance.
```

## Updating Provenance Over Time

As understanding evolves:

1. **Add new sources** — When you discover additional original sources, add them to the provenance section
2. **Document refinements** — Record how techniques have been improved based on community feedback or research
3. **Note deprecations** — When approaches become outdated, document why and what replaced them
4. **Credit contributors** — Acknowledge people who have improved or extended documented concepts

Example evolution:

```markdown
## Provenance and Evolution

### Original Concept (2024)
- Source: Matt Pocock
- Documented as: Simple 6-step feedback loop

### First Refinement (2024, Month X)
- Community feedback noted blocker detection wasn't always clear
- Added explicit blocker categories (compilation error, test failure, etc.)
- Source: [Practitioner names/GitHub issues]

### Current Version
- Further clarified token usage and context window management
- Added examples showing common blocker scenarios
- See [Contributing](../../AGENTS.md) to suggest improvements
```

## Validation Checklist

Before publishing content, verify:

- [ ] **Provenance section exists** (if concept warrants it)
- [ ] **All major sources are credited** 
- [ ] **Source types are specified** (Person, Article, Experiment, etc.)
- [ ] **URLs are provided** where source material is publicly available
- [ ] **Context explains the source's contribution** (don't just list names)
- [ ] **Experimental validation is documented** (if applicable)
- [ ] **Related concepts are cross-linked** in the main handbook
- [ ] **No sources are overlooked** (especially people and publications)

## FAQ

**Q: How much detail is enough?**
A: Enough that a reader could inspect the source material themselves if interested. At minimum: 
source name, source type, and URL (if available).

**Q: Should I cite blog posts and videos?**
A: Yes. If a blog post or video materially influenced the documented concept, cite it.

**Q: What if the source is conversation?**
A: Document it as "Personal Communication" or "Conversation with [person]" with approximate date.

**Q: Should examples reference the canonical pattern's provenance?**
A: Yes. Cross-link to the canonical pattern. Platform/harness-specific examples inherit 
provenance from the canonical source.

**Q: Can we update provenance after publication?**
A: Yes. Document updates as refinements over time. Use Git history to track evolution.

**Q: What if someone disagrees with attribution?**
A: Document the disagreement and multiple perspectives if they're substantively different. 
The handbook should support evolution and conflicting views until consensus emerges.

---

See [README.md](README.md) for overall reference and cross-linking strategy.
