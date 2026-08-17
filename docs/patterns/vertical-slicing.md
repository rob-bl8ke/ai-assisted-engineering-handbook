# Vertical Slicing

A technique for decomposing work into thin, end-to-end units that can be independently tested, reviewed, and deployed.

---

## Pattern Definition

Vertical slicing breaks a feature or body of work into smaller pieces that span all layers of the system (database, business logic, UI, API, etc.), rather than splitting by technical layer (all database changes, then all logic, then all UI).

**Vertical slice:** A complete, testable unit that delivers some tangible value or unblocks other work.

**Contrast with horizontal slicing:** Breaking work into technical layers (architecture, database schema, backend logic, API endpoints, frontend views) that must be completed in sequence before the feature is usable.

---

## When to Use Vertical Slicing

**Use vertical slicing when:**
- You want to parallelize work and reduce dependencies
- You need to validate assumptions early
- You want to integrate frequently and catch integration issues early
- You're building something new and uncertain about requirements
- You want clear progress visibility (each slice is deployable)

**Don't use vertical slicing when:**
- Work is inherently sequential and tightly coupled
- The scope is too small (< 1 hour of work)
- Architectural changes must precede feature work
- Infrastructure work has no feature applicability

---

## How It Works

### Step 1: Understand the Full Design

Before slicing, ensure you understand:
- Complete system architecture
- How the feature affects all layers
- All dependencies and integration points
- Success criteria and acceptance tests

**Example:** Building a user registration feature requires understanding:
- How user data is stored (database/schema)
- Authentication mechanism (validation/hashing/tokens)
- API endpoints needed
- UI/form requirements
- How this integrates with existing account systems

### Step 2: Identify the Thinnest Slice

Start with the smallest possible vertical slice that:
- **Spans all layers** — Includes at least database, logic, and interface
- **Is independently testable** — Can be tested in isolation
- **Delivers some value** — Unblocks other work or proves the approach
- **Is small** — Ideally completable in 1-4 hours

**Example:** Rather than "build user registration" (1-2 days), start with:
- "User can enter email and password in form, submit, and see validation feedback" (3-4 hours)
  - Includes: Form UI, validation logic, error display
  - Can be tested standalone
  - Proves the form approach works

### Step 3: Build the Slice Completely

For each slice:
- Implement all layers needed (don't defer UI or don't defer logic)
- Write tests that verify the slice works end-to-end
- Ensure the slice is deployable (doesn't break existing systems)
- Document any decisions or future work revealed

### Step 4: Integrate and Validate

- Integrate the slice into main development branch
- Verify it works in the broader system
- Run regression tests
- Get feedback (QA, users, stakeholders)

### Step 5: Plan Next Slice Based on Feedback

The feedback from each slice often shapes the next slice:
- Changed requirements → adjust next slice
- Technical discoveries → inform architectural decisions
- Performance issues → add optimization slices
- New edge cases → add handling slices

---

## Characteristics of Well-Sliced Work

A good vertical slice has these properties:

| Property | Description | Why It Matters |
|----------|-------------|----------------|
| **End-to-end** | Touches at least database, logic, interface | Proves the approach works across all layers |
| **Independently testable** | Can write passing tests for the slice alone | Enables parallel work and early validation |
| **Independently reviewable** | A single person can understand it completely | Simplifies code review and feedback |
| **Independently deployable** | Can merge to main without breaking systems | Enables frequent integration and rapid feedback |
| **Valuable or blocking** | Either provides user value or unblocks other work | Maintains forward momentum and visibility |
| **Small** | Doable in hours, not days | Enables rapid iteration and feedback loops |

---

## Common Pitfalls

### ❌ False Vertical Slices (Actually Horizontal)

**Problem:** Claiming a slice is "vertical" when it's actually a horizontal layer.

**Example (wrong):**
- Slice 1: "Create user database schema"
- Slice 2: "Create authentication endpoints"
- Slice 3: "Create login form UI"

These slices are horizontal (one per layer). Users can't test or use Slice 1 or 2 independently.

**Fix:** Reframe as vertical:
- Slice 1: "User can register with email/password; see validation errors" (includes all layers)
- Slice 2: "User can log in; receives authentication token" (uses existing schema, adds new flows)
- Slice 3: "User can reset password; receives reset link via email" (extends existing flows)

### ❌ Slices That Are Too Large

**Problem:** Slice is too ambitious, not completed quickly, causes blocking.

**Example (wrong):** 
- "Implement complete user management system" (5+ days)

**Fix:** Make slices smaller:
- Slice 1: "User can sign up with email/password" (4 hours)
- Slice 2: "User can log in and receive token" (4 hours)
- Slice 3: "User can reset password" (4 hours)
- etc.

### ❌ Slices That Skip Layers

**Problem:** Skipping one layer under the assumption it will be filled in later.

**Example (wrong):**
- "Create API endpoints" without testing that UI can call them
- "Create form UI" without backend validation

**Fix:** Include all layers, even if minimally:
- API slice: Create endpoints, add simple tests, don't worry about performance
- Form slice: Create form, wire to existing API, add validation display

### ❌ Ignoring Dependencies

**Problem:** Creating slices that create blockers for other work.

**Example (wrong):**
- Slice 1: "Refactor authentication system" (blocks all auth-related work)
- Slice 2: "Add new login method"

**Fix:** Order slices to minimize blockers:
- Slice 1: "Add new login method alongside existing auth" (no refactoring)
- Slice 2: "Create abstraction layer" (only after new method works)
- Slice 3: "Refactor to use abstraction"

---

## Vertical Slices vs. Tracer Bullets

**Vertical Slicing** is the decomposition strategy (how you break up work).

**Tracer Bullets** is a complementary approach (how you implement each slice).

**Together they mean:**
- Use vertical slicing to plan independent units of work
- Use tracer bullets to implement each unit with minimal, end-to-end code
- Refactor and expand tracer bullets in subsequent slices

See [Tracer Bullets](tracer-bullets.md) for details on that pattern.

---

## Vertical Slices vs. Horizontal Work

Some work is inherently horizontal and doesn't fit vertical slicing:

| Work Type | How to Handle |
|-----------|---------------|
| **Infrastructure** (new database, new service) | Create as prerequisite; don't try to slice; verify it works before feature slices |
| **Architectural refactoring** | Either slice (refactor one area while maintaining others) or do it as prerequisite |
| **Dependency upgrades** | Usually prerequisite; rarely can be sliced |
| **Technical spike** (investigation) | Time-boxed exploration; don't slice; either confirms feasibility or guides new approach |

The key difference: **Horizontal work blocks other work; vertical slices can be done in parallel.**

---

## Benefits

- **Parallelization:** Multiple people can work on independent slices
- **Early Feedback:** Feedback after each slice shapes subsequent work
- **Integration Confidence:** Regular integration catches issues early
- **Risk Reduction:** Validate approach before investing in large implementation
- **Progress Visibility:** Each slice is tangible progress
- **Deployment Frequency:** Slices can be deployed as completed
- **Reduced Rework:** Early feedback prevents building the wrong thing

---

## Implementation with AI Agents

Vertical slicing is particularly powerful with AI agents:

1. **Clear Scope:** Each slice has explicit boundaries and acceptance criteria
2. **Independent Execution:** An agent can work on one slice without waiting for others
3. **Testability:** Success criteria are unambiguous and automatable
4. **Feedback Integration:** Agent can read feedback from previous slice and adapt

A well-formed vertical slice is a good target for a [Goal Loop](goal-loop/README.md): the slice has a bounded completion condition, testable behavior, and a clear Definition of Done.

When the slice should be implemented test-first with specialized agents, use a [TDD Goal Loop](tdd-goal-loop/README.md) to coordinate test writing, red verification, minimal code implementation, green verification, slice verification, and final goal evaluation.

The software development process uses vertical slicing in [Phase 3: Decomposition](../processes/software-development.md#phase-3-decomposition) to create issues that agents can execute independently.

---

## Related Patterns

- **[Goal Loop](goal-loop/README.md)** — Prove a slice's Definition of Done with observable evidence
- **[TDD Goal Loop](tdd-goal-loop/README.md)** — Implement a slice through coordinated red/green agent roles
- **[Tracer Bullets](tracer-bullets.md)** — Implement each slice with minimal, end-to-end code
- **[Feedback Loops](feedback-loops.md)** — Use feedback from each slice to guide the next
- **[Test-Driven Development](test-driven-development.md)** — Test each slice thoroughly

---

## References

- Martin Fowler on continuous delivery and vertical slicing
- Agile development practice of thin vertical slices
- Lean software development principles

---

**Last Updated:** 2026-08-16  
**Status:** Core Pattern Documentation (MVP)
