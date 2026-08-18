# Context Management

A technique for deliberately controlling what information is provided to an AI agent at each step, balancing completeness (enough information to work) with conciseness (not overwhelming the agent).

---

## Pattern Definition

Context management is about intentionally deciding:

1. **What information** the AI needs (requirements, design, code samples, constraints)
2. **How much detail** to provide (complete or summarized)
3. **What to exclude** (irrelevant systems, outdated documentation, noise)
4. **When to refresh** (before each step or when requirements change)

Good context management means the AI can work effectively without wasting LLM tokens on irrelevant information.

For large repositories, [Repository Mental Model Tools](../references/repository-mental-model-tools.md) can support this pattern by selecting relevant structural context: callers, dependencies, affected symbols, architecture relationships, and cross-artifact links.

For multi-repository systems, the [Unified Agent Workspace](unified-agent-workspace.md) pattern creates a shared filesystem boundary, instructions layer, and engineering index so agents can manage context across independently versioned repositories.

---

## Why Context Management Matters

### The Problem: Context Overload

```
❌ Bad: Provide everything
  - Entire codebase (100k lines)
  - All documentation (20 documents)
  - All related code (5 systems)
  - AI is lost: "Where do I start?"
  - Wastes tokens: 80% is irrelevant
  - AI gets confused: Contradictory information
```

### The Solution: Intentional Context

```
✓ Good: Provide relevant context
  - File structure overview
  - Relevant code samples (100-200 lines)
  - Design documentation for this task
  - Acceptance criteria
  - Links to more detailed docs if needed
  - AI understands immediately
  - Efficient token usage
  - Clear success criteria
```

---

## When to Use Context Management

**Always.** Every interaction with an AI agent should include thoughtful context management.

Regardless of whether the AI is:
- Working independently (AFK workflow)
- Working with human guidance (supervised workflow)
- Answering a question
- Implementing a feature
- Reviewing code

---

## How It Works

### Step 1: Identify What Information Is Needed

Before engaging an AI agent, ask: **What does the AI need to succeed?**

#### For Implementation Tasks

```
Required Information:
  ✓ What to build (user story, requirements, acceptance criteria)
  ✓ How it fits in the system (architecture, relevant code samples)
  ✓ Testing expectations (what counts as success)
  ✓ Constraints (performance, security, compatibility)
  ✓ Where to find answers (relevant documentation, existing code)

Not Required:
  ✗ Entire codebase
  ✗ Unrelated features
  ✗ Historical notes
  ✗ Brainstorming discussions
```

#### For Review/Analysis Tasks

```
Required Information:
  ✓ What to analyze (specific code, PR, change)
  ✓ Context (what the change should do, why it matters)
  ✓ Standards (how to evaluate, what matters)
  ✓ Related code (how it integrates)

Not Required:
  ✗ Team politics
  ✗ Unrelated systems
  ✗ Implementation history
  ✗ Future roadmap (unless relevant)
```

### Step 2: Gather Relevant Information

Collect the necessary information:

#### Code Context
- **Relevant files** (10-20 files, not 1000)
- **Key functions/classes** that interact with the work
- **Test examples** showing patterns
- **Configuration** affecting the work

#### Documentation Context
- **Requirements/specification** (what, not how)
- **Design decisions** (why this approach)
- **Constraints** (performance, security, compatibility)
- **Existing patterns** (how similar work is done)

#### System Context
- **Architecture overview** (how systems connect)
- **Data flow** (how information moves)
- **API contracts** (what can the AI call)
- **Integration points** (what can break if changed)

### Step 3: Organize Information Clearly

Present context in a hierarchy:

```
1. PRIORITY 1: Acceptance Criteria (what success looks like)
2. PRIORITY 2: Key Code Samples (how it's structured)
3. PRIORITY 3: Design Constraints (what matters most)
4. PRIORITY 4: Related Context (how it fits in)
5. LINKS: Detailed docs (if AI needs more detail)
```

**Example for an implementation task:**

```
TASK: Implement user search feature

ACCEPTANCE CRITERIA:
- User can search for accounts by name
- Results show name, email, account type
- Search is case-insensitive
- Empty search returns empty results
- Max 100 results returned

KEY CODE:
- See AccountService.findByName() (example of existing search)
- See AccountController.search() (API endpoint pattern)
- See SearchTest (test pattern for search features)

CONSTRAINTS:
- Search must complete in < 500ms
- Cannot use expensive LIKE queries; use full-text search indexes
- Must maintain backward compatibility with existing API

ARCHITECTURE:
- AccountService (business logic) → AccountRepository (data access)
- AccountController (HTTP API) → AccountService
- See docs/architecture/search.md for full design

NEXT STEPS:
If you need more detail, see:
- docs/database/schema.md
- docs/api/contracts.md
```

### Step 4: Make It Easy to Ask for More

Include references to additional information:

```
"If you need more context on [topic], see [doc/file]"
"If you're stuck on [problem], that's discussed in [reference]"
"Questions? Post to [channel] or see [FAQ]"
```

### Step 5: Refresh Context Between Phases

As work progresses, update context:

```
Phase 1: Discovery
  ✓ Provide: Problem statement, user needs, constraints
  
Phase 2: Design
  ✓ Provide: Architectural decisions, API design, data schema
  
Phase 3: Decomposition
  ✓ Provide: Acceptance criteria for each slice, dependencies
  
Phase 4: Implementation
  ✓ Provide: This specific slice's requirements, related code
  ✓ Refresh: If requirements changed or new patterns discovered
  
Phase 5: Validation
  ✓ Provide: Success criteria, how to test, what to look for
```

---

## Context Management Principles

### ✓ Include Relevant Information

Include information that:
- Directly relates to the task
- Helps the AI understand constraints
- Shows patterns to follow
- Defines success

### ✗ Exclude Irrelevant Information

Exclude:
- Unrelated systems or features
- Historical notes or cancelled ideas
- Brainstorming or exploratory discussions
- Long walls of text that obscure key info

### ✓ Be Explicit About Priorities

Make clear what matters most:
- Acceptance criteria (most important)
- Performance constraints
- Security requirements
- Compatibility requirements

### ✓ Use Examples

Show:
- Similar existing code
- Test patterns
- Design patterns used in codebase
- Error handling examples

### ✓ Make It Scannable

Format for quick understanding:
- Headings (what section is this?)
- Bullet points (key facts)
- Code blocks (actual code examples)
- Links (where to find more)

### ✗ Bury Critical Information

Don't hide important information:
- ✗ "Oh, one more thing..." (put critical info at top)
- ✗ Long paragraphs (use bullets and headings)
- ✗ Embedded in historical context (separate what matters now)

---

## Common Pitfalls

### ❌ Context Overload: Dumping Everything

**Problem:** Providing the entire codebase, all documentation, all related systems.

**Impact:**
- AI gets confused by contradictory information
- Critical constraints are buried
- Wastes LLM tokens on irrelevant detail
- Takes AI longer to understand what matters

**Fix:** Be ruthless about relevance:
```
❌ "Here's our entire auth system code"
✓ "Here's the UserService (3 methods we use), see auth/patterns.md for design"
```

### ❌ Context Underload: Missing Critical Information

**Problem:** Providing too little context; AI has to guess or ask questions.

**Impact:**
- AI makes wrong assumptions
- Implementation doesn't match needs
- Requires multiple iterations to get right

**Fix:** Include the minimum complete set:
```
❌ "Implement user search"
✓ "Implement user search with acceptance criteria: [list], constraints: [list], see SearchTest for pattern"
```

### ❌ Outdated Context

**Problem:** Providing old documentation or code samples that no longer reflect reality.

**Impact:**
- AI follows wrong patterns
- Implementation conflicts with current code
- Requires rework

**Fix:** Refresh context regularly:
- Mark when documentation was last verified
- Remove outdated patterns
- Link to current examples

### ❌ Implicit Constraints

**Problem:** Not stating constraints explicitly.

**Example (wrong):**
```
"Implement the payment feature"
(Implicit: must be PCI compliant, use this payment provider, 
 return results in 100ms, no breaking changes)
```

**Fix:** State constraints explicitly:
```
"Implement the payment feature

CONSTRAINTS:
- Must be PCI compliant (see docs/security/pci.md)
- Must use Stripe API (see docs/integrations/stripe.md)
- Must complete payment in < 100ms
- Backward compatible with existing payment endpoints
```

### ❌ Context Without Connection

**Problem:** Providing information that isn't clearly connected to the task.

**Example (wrong):**
```
"Here's our codebase overview, design patterns doc, 
 architecture diagram, team structure, and CI/CD setup"
(What does AI do with this?)
```

**Fix:** Connect information to the task:
```
"To implement this feature you need to know:

1. How users are stored (see schema in database/users.md)
2. How existing searches work (see SearchService and SearchTest)
3. Performance targets (< 500ms, max 100 results)
4. Where to integrate (see PaymentController at line 42)
```

---

## Context Management with AI Agents

Context management is essential for AI agents:

### AFK Workflows
- Provide complete context upfront (agent works independently)
- Include: requirements, architecture, patterns, test examples
- Minimize the need for the agent to ask questions

### Supervised Workflows
- Provide context for current step
- Refresh context as AI asks questions
- Clarify when AI makes wrong assumptions

### Feedback Loops
- After AI completes a slice, you understand what context was missing
- Use that learning to improve context for next slice
- Refine context based on questions the AI asked

---

## Benefits

- **Efficiency:** AI works effectively without wasted context
- **Accuracy:** Clear context reduces misunderstandings
- **Speed:** AI doesn't need to ask clarifying questions
- **Cost:** Fewer LLM tokens wasted on irrelevant information
- **Maintainability:** Clear context helps humans maintain the work
- **Consistency:** All agents work from the same clear context

---

## Related Patterns

- **[Vertical Slicing](vertical-slicing.md)** — Each slice has its own focused context
- **[Tracer Bullets](tracer-bullets.md)** — Start with minimal context; add detail as needed
- **[Feedback Loops](feedback-loops.md)** — Use feedback to improve context for next iteration

---

## References

- "The Pragmatic Programmer" on context and clarity
- LLM best practices on prompt engineering
- Agile documentation principles (just enough, just in time)

---

**Last Updated:** 2026-08-16  
**Status:** Core Pattern Documentation (MVP)
