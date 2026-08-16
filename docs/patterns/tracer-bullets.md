# Tracer Bullets

A technique for implementing a feature by building a thin, end-to-end path through all system layers, starting with the simplest possible implementation that demonstrates the approach works.

---

## Pattern Definition

Tracer bullets work like ammunition that leaves a visible path showing where subsequent shots should land. In software engineering:

**Tracer bullet:** A minimal, end-to-end implementation that proves the approach works, reveals obstacles, and guides refinement.

Rather than building all the infrastructure first, then all the logic, then all the UI, tracer bullets build one thin path that spans everything, working minimally at each layer. Subsequent refinement adds robustness, error handling, performance, and edge cases.

---

## The Approach

### Tracer Bullet Implementation

```
┌─────────────────────────────────────────┐
│          User Interface                  │
│        (simple, minimal UI)              │
├─────────────────────────────────────────┤
│       Business Logic / Algorithm         │
│     (basic, no error handling yet)       │
├─────────────────────────────────────────┤
│         Data Storage & APIs              │
│      (simple schema, basic endpoints)    │
└─────────────────────────────────────────┘

This is tracer bullet v1: simple, complete, working
```

### Refinement Passes

```
Pass 1: Tracer bullet (thin path, minimal everything)
  ↓
Pass 2: Add error handling and validation
  ↓
Pass 3: Add performance optimization
  ↓
Pass 4: Add edge cases and robustness
  ↓
Pass 5: Polish UI/UX
```

---

## When to Use Tracer Bullets

**Use tracer bullets when:**
- You're building something new or unfamiliar
- Requirements are not fully clear
- You want to validate architectural approach
- You need rapid iteration and feedback
- The cost of being wrong is high (wrong tech stack, wrong approach)

**Combine with vertical slicing:**
- Vertical slicing breaks work into independent units
- Each unit is implemented using tracer bullets
- After each tracer bullet is working, refine it and move to the next unit

---

## How It Works

### Step 1: Identify What Proof You Need

Before implementing, define the minimal goal:

**Example (user registration):**
- Proof needed: "A user can enter an email and password through a form and see it's accepted"
- Not needed yet: secure password storage, email validation, duplicate checking, password strength, UI polish

**Example (payment feature):**
- Proof needed: "User can enter card details and complete a transaction"
- Not needed yet: PCI compliance, fraud detection, refund handling, performance optimization

### Step 2: Build the Minimal Path

Implement each layer minimally:

| Layer | Tracer Bullet | Later Refinement |
|-------|--------------|------------------|
| **UI** | Simple form, basic styling | Responsive design, accessibility, UX polish |
| **Logic** | Basic validation, happy path | Error handling, edge cases, business rules |
| **Storage** | Simple schema, hardcoded values | Performance indexes, migration strategy, backup |
| **API** | Minimal endpoints, no security | Auth/authorization, rate limiting, versioning |

### Step 3: Test the Tracer Bullet End-to-End

Write tests that verify the complete path works:

```
Test: User can register
  1. Load form ✓
  2. Enter email and password ✓
  3. Submit form ✓
  4. See confirmation ✓
  5. Data is persisted ✓
```

**Don't test refinements yet:**
- ✗ Invalid email formats
- ✗ Weak passwords
- ✗ Duplicate emails
- ✗ Database error handling
- ✗ Concurrent requests

### Step 4: Show It Works

Demonstrate the tracer bullet:
- It's actually working (not a prototype)
- It spans all layers (not just one layer)
- You learned something about the approach
- You have a foundation for refinement

### Step 5: Refine in Focused Passes

After the tracer bullet works, add refinement in focused areas:

**Pass 1 - Validation:**
- Add email format validation
- Add password strength requirements
- Show error messages

**Pass 2 - Robustness:**
- Handle duplicate email registration
- Add transaction handling
- Add logging

**Pass 3 - Performance:**
- Add indexes to frequently-queried columns
- Add caching
- Optimize queries

Each refinement pass:
- Focuses on one concern (validation, robustness, performance)
- Adds tests for that concern
- Doesn't break the basic functionality

---

## Key Principle: Working Software, Not Perfect Software

Tracer bullets prioritize:
1. **Working** — The path is complete and testable
2. **Visible** — You can see it work end-to-end
3. **Instructive** — It reveals what you need to build next
4. **Minimal** — Every unnecessary piece is removed

Tracer bullets explicitly delay:
- Edge cases
- Error conditions
- Performance optimization
- Security hardening
- User experience polish

**This is intentional.** Perfect code for features you don't fully understand is wasteful. Tracer bullets find the real requirements first.

---

## Common Pitfalls

### ❌ Tracer Bullets That Don't Span Layers

**Problem:** Building a partial path that skips a layer.

**Example (wrong):**
- "I'll implement the form first, then come back to the backend later"
- "I'll mock the API so the UI can be tested, then implement the API"

**Fix:** Include all layers, even minimally:
- Form + real backend (even if backend is trivial)
- Real data storage (even if it's a simple list or hardcoded values)
- Real API calls (even if the API returns hardcoded responses initially)

### ❌ Tracer Bullets That Get Abandoned

**Problem:** Building a tracer bullet and then starting over from scratch instead of refining it.

**Example (wrong):**
- "The tracer bullet is too messy; let me rewrite it properly from the beginning"

**Fix:** Treat the tracer bullet as the foundation and refine it:
- The basic structure is proven to work
- Refine the existing code rather than rewriting
- Document why the original approach was correct and what refinements add

### ❌ Confusing Tracer Bullets with Prototypes

**Problem:** Tracer bullets should be real, working code. Prototypes are throw-away.

**Example (wrong):**
- Building a tracer bullet with hardcoded values, then throwing it away
- Building UI mockups that don't connect to real data

**Fix:** Build tracer bullets to be kept and refined:
- Use real data persistence (even if simple)
- Use real API calls (even if endpoints are minimal)
- Keep and refine the tracer bullet code

---

## Tracer Bullets vs. Vertical Slicing

**Vertical slicing** is about decomposing work into independent units.

**Tracer bullets** is about how to implement each unit.

**Together they mean:**
- Each vertical slice is implemented using the tracer bullet approach
- Each vertical slice starts minimal and spans all layers
- Subsequent slices refine and add capabilities

---

## Tracer Bullets with AI Agents

Tracer bullets are particularly useful with AI:

1. **Clear Definition:** "Build a tracer bullet that does X" is unambiguous
2. **Testability:** Success is visible and testable, not subjective
3. **Feedback Opportunity:** After tracer bullet works, humans can guide refinement
4. **Risk Reduction:** Wrong approach is discovered early, before heavy refinement

The software development process uses tracer bullets implicitly in [Phase 4: Implementation](../processes/software-development.md#phase-4-implementation), particularly in the [AFK workflow](../processes/software-development.md#42-afk-implementation-ralph-loop).

---

## Benefits

- **Fast Feedback:** Know if your approach works in hours, not days
- **Early Learning:** Discover requirements and obstacles quickly
- **Reduced Rework:** Wrong approaches are corrected before heavy investment
- **Clear Refinement Path:** Each tracer bullet suggests what to refine next
- **Confidence:** Stakeholders see working software early
- **Flexibility:** Easy to pivot if tracer bullet reveals better approach
- **Documentation:** Working code is the best documentation of approach

---

## Example: Building a Search Feature

### Tracer Bullet v1 (2 hours)
- Form with search box
- Backend returns all items (no filtering)
- Results display in a list
- **Test:** User can type in box, results appear
- **Reveal:** Displays work across UI and backend

### Refinement Pass 1 (2 hours)
- Add actual filtering logic
- Add search result ranking
- Show match highlighting
- **Test:** Relevant results appear first

### Refinement Pass 2 (4 hours)
- Add performance optimization (indexes, caching)
- Handle empty results
- Handle large result sets (pagination)
- **Test:** Search performs well with large datasets

### Refinement Pass 3 (2 hours)
- Polish UI (responsive, accessibility)
- Add advanced search options
- Add search history

---

## Related Patterns

- **[Vertical Slicing](vertical-slicing.md)** — Organize work into independent units; implement each with tracer bullets
- **[Feedback Loops](feedback-loops.md)** — Use feedback after each tracer bullet to guide refinement
- **[Test-Driven Development](test-driven-development.md)** — Test tracer bullets to verify they work end-to-end

---

## References

- "The Pragmatic Programmer" by Hunt and Thomas (original tracer bullet concept)
- Agile development practices (build incrementally, test continuously)
- Lean software development (eliminate waste, deliver value early)

---

**Last Updated:** 2026-08-16  
**Status:** Core Pattern Documentation (MVP)
