# References, Provenance, and Cross-Linking

This directory documents where important ideas, patterns, and processes originated, and how they interconnect within the handbook.

## Purpose

The handbook should answer not only:

> What is our current approach?

but also:

> Where did this idea come from?
>
> Who contributed to evolving this concept?
>
> What related ideas should I also consider?

## Understanding Provenance

Every significant concept, pattern, process, or technique documented in the handbook should ideally trace its origins back to:

- **People** — Practitioners and researchers who developed or popularized the approach
- **Publications** — Articles, videos, transcripts, conference talks, or documentation
- **Experiments** — Internal testing that validated or adapted an approach
- **Practitioners** — Real-world experience from engineers using these techniques

Provenance serves multiple purposes:

1. **Credit** — Acknowledges the originating source and respects intellectual property
2. **Research** — Allows readers to inspect original material and deeper context
3. **Validation** — Shows that techniques come from proven practice, not speculation
4. **Evolution** — Enables us to track how ideas have changed and improved
5. **Judgment** — Helps readers assess confidence levels in different approaches

## Recording Provenance

Every handbook document should include a **Sources** or **Provenance** section that records:

1. **Source Title** — The name, article title, video title, or reference
2. **Source Type** — Person, Article, Video, Transcript, Experiment, Tool Documentation, GitHub Repository, Other
3. **Source URL** (if applicable) — Where the source material can be found
4. **Context** — Brief explanation of what this source contributed to the concept
5. **Date Accessed** (if applicable) — When the source was reviewed
6. **Notes** — Any relevant context about how this source influenced the handbook

Example format:

```markdown
## Sources and Provenance

| Source | Type | Context | URL |
|--------|------|---------|-----|
| "Coding with AI" talk | Video | Initial workflow inspiration | https://example.com/video |
| Internal Ralph Loop experiments | Experiment | Validated Bash to PowerShell portability | N/A |
| Claude Code documentation | Documentation | Claude-specific execution environment details | https://docs.anthropic.com |
```

See [provenance.md](provenance.md) for detailed guidance.

## Tool Ecosystem References

- [Agent Context Artifacts](agent-context-artifacts.md) - Decision model for when to use `AGENTS.md`, `CONTEXT.md`, skills, and task state
- [Repository Mental Model Tools](repository-mental-model-tools.md) - MCP, AGENTS.md, Serena, code-review-graph, Graphify, Sense, Codebase-Memory, and related tools for portable repository context and code intelligence

## Cross-Linking Strategy

Handbook concepts frequently build on or relate to one another. Rather than duplicating explanations, concepts should link to related knowledge.

### Categories of Relationships

1. **Prerequisite** — Understanding X helps you understand Y
   - Example: "Ralph Loop" → "Understand Feedback Loops" (see Patterns > Feedback Loops)

2. **Composition** — Y is composed of multiple X concepts
   - Example: "Feature Development Playbook" → uses "PRD Creation Skill", "Vertical Slicing Pattern", "Ralph Loop Pattern", "Code Review Playbook"

3. **Alternative** — Y is an alternative approach to X (with different trade-offs)
   - Example: "Ralph Loop Pattern" ↔ "Traditional QA Waterfall" (canonical link goes one direction)

4. **Platform/Harness Variant** — Y is a platform or harness-specific implementation of X
   - Example: "Ralph Loop Pattern" → Bash Example, PowerShell Example, Claude Adapter, Copilot Adapter

5. **Anti-Pattern** — Y is what NOT to do when using X
   - Example: "Context Economy Principle" → Anti-pattern: "Loading entire codebase into context"

6. **Related Reading** — Y provides additional context or perspective on X
   - Example: "Test-Driven Development" → also see "Continuous Feedback" pattern

### Cross-Linking in Practice

At the end of relevant handbook documents, include a section such as:

```markdown
## Related Concepts

**Patterns:**
- [Vertical Slicing](../patterns/vertical-slicing.md) — How to break down work
- [Test-Driven Development](../patterns/test-driven-development.md) — Testing approach
- [Feedback Loops](../patterns/feedback-loops.md) — How iteration works

**Processes:**
- [Feature Development](../processes/software-development.md#feature-development) — Complete workflow

**Playbooks:**
- [Decompose Work](../playbooks/decompose-work.md) — How to apply vertical slicing
- [Repository Exploration](../playbooks/repository-exploration.md) — Understand codebase first

**Skills:**
- [Write PRD](../skills/write-prd.md) — Document requirements
```

## Cross-Link Index

The following table shows major concept relationships to ensure comprehensive cross-linking:

| Source Concept | Type | Links To | Link Type |
|---|---|---|---|
| Ralph Loop | Pattern | Feedback Loops Pattern | Prerequisite |
| Ralph Loop | Pattern | TDD Pattern | Prerequisite |
| Ralph Loop | Pattern | Feature Development Process | Composition |
| Ralph Loop | Pattern | Bash Example | Platform |
| Ralph Loop | Pattern | PowerShell Example | Platform |
| Ralph Loop | Pattern | Claude Adapter | Harness |
| Scheduled Agentic Work | Pattern | Sync Skills With Upstream Repositories Playbook | Application |
| Scheduled Agentic Work | Pattern | Unified Agent Workspace Pattern | Composition |
| Scheduled Agentic Work | Pattern | Context Management Pattern | Prerequisite |
| Scheduled Agentic Work | Pattern | Repository Mental Model Tools Reference | Support |
| Scheduled Agentic Work | Pattern | Claude, Copilot, OpenCode, and GitHub Agentic Workflows | Harness variants |
| Scheduled Agentic Work | Pattern | Skills README | Runtime portability |
| TDD Goal Loop | Pattern | TDD Goal Loop Claude Spring Boot Lab | Runnable prototype |
| TDD Goal Loop Claude Spring Boot Lab | Playbook | TDD Goal Loop Claude Adapter | Harness application |
| Agent Context Artifacts | Reference | Context Management Pattern | Prerequisite |
| Agent Context Artifacts | Reference | Skills README | Boundary definition |
| Agent Context Artifacts | Reference | Repository Mental Model Tools Reference | Support |
| Agent Context Artifacts | Reference | Unified Agent Workspace Pattern | Composition |
| Agent Workspace Lab | Playbook | Scheduled Agentic Work Pattern | Application |
| Agent Workspace Lab | Playbook | Unified Agent Workspace Pattern | Validation |
| Agent Workspace Lab | Playbook | Skills README | Portability test |
| Vertical Slicing | Pattern | Feature Development Process | Composition |
| Vertical Slicing | Pattern | Decompose Work Playbook | Composition |
| Feature Development | Process | Create PRD Playbook | Composition |
| Feature Development | Process | Vertical Slicing Pattern | Prerequisite |
| Feature Development | Process | Ralph Loop Pattern | Prerequisite |
| PRD to Issues | Skill | Feature Development Process | Composition |
| PRD to Issues | Skill | Write PRD Skill | Prerequisite |
| Code Review | Playbook | Review Implementation Playbook | Alternative |
| Repository Exploration | Playbook | Feature Development Process | Prerequisite |

## Identifying Missing Links

As new content is added to the handbook, ask:

- [ ] Does this concept depend on understanding another concept first?
- [ ] Is this concept part of a larger workflow?
- [ ] Are there alternative approaches that readers should be aware of?
- [ ] Does this concept have platform-specific or harness-specific implementations documented elsewhere?
- [ ] Are there related playbooks or skills that apply this pattern?
- [ ] What sources document this concept?

If answers reveal gaps, add the appropriate cross-links.

## Validation Checklist

When adding content, verify:

- [ ] Provenance is recorded (see [provenance.md](provenance.md))
- [ ] Related patterns are linked in a "Related Concepts" section
- [ ] Playbooks that use this pattern/skill/process are identified
- [ ] Alternative approaches are acknowledged (if applicable)
- [ ] Platform/harness-specific implementations are cross-linked
- [ ] The document is discoverable from main README.md
- [ ] No content is duplicated unnecessarily (enrich existing docs first)

## Examples of Good Cross-Linking

### Example 1: Ralph Loop Pattern

The canonical Ralph Loop pattern document includes:
- A "Sources" section crediting key influences
- Links to Bash and PowerShell examples
- Links to Claude, Copilot, and OpenCode adapters
- A link to "Feedback Loops" pattern as a prerequisite
- Links to Feature Development process where Ralph Loop is applied
- A "See Also" section with related patterns (Vertical Slicing, TDD)

### Example 2: Feature Development Process

The Feature Development process includes:
- A "Sources" section crediting key methodology influences
- Composition links to:
  - Create PRD Playbook
  - Vertical Slicing Pattern
  - Ralph Loop Pattern
  - Code Review Playbook
- Links to each playbook/pattern showing how it fits in the workflow
- A "See Also" section with alternative approaches (bug fixes, refactoring)

## Future Enhancements

Potential improvements (not MVP requirements):

1. **Knowledge Graph Visualization** — Automated diagram showing concept relationships
2. **Confidence Ratings** — Distinguish well-established approaches from experimental ones
3. **Source Freshness Checks** — Track when sources were last verified
4. **Duplicate Detection** — Automated identification of duplicated concepts
5. **Link Validation** — Automated checking that cross-links are correct
6. **Deprecation Tracking** — Documentation of why older approaches are no longer recommended
7. **Evidence Rating** — Distinguish between widely-used practices, academic research, and early experiments

---

See [provenance.md](provenance.md) for detailed provenance recording guidance.

See [AGENTS.md](../../AGENTS.md) for how AI agents maintain cross-links and provenance.
