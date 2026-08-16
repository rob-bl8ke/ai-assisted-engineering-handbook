# Feedback Loops

A technique for continuously incorporating information from completed work back into planning and refinement, improving outcomes through rapid iteration.

---

## Pattern Definition

Feedback loops are the mechanism by which you learn from work and apply that learning immediately.

**Feedback loop:** After completing a unit of work, capture what you learned, and use it to guide the next unit.

This creates continuous improvement:

```
Plan Work
   ↓
Execute Work
   ↓
Observe Outcomes ← FEEDBACK
   ↓
Learn from Outcomes
   ↓
Refine Plan
   ↓
Plan Next Work (improved)
```

---

## Why Feedback Loops Matter

Without feedback loops:
- You make the same mistakes repeatedly
- Requirements misunderstandings compound
- Design issues only appear late
- Work goes in wrong direction until caught in review

With feedback loops:
- Issues are caught and corrected immediately
- Requirements are refined incrementally
- Design is validated before heavy investment
- Work stays aligned with actual needs

---

## Types of Feedback Loops

### 1. Implementation Feedback

**After building a tracer bullet or slice:**

```
Questions to ask:
- Did the approach work as expected?
- What surprised you?
- Did you learn anything about the requirements?
- Did you discover edge cases?
- What would you do differently next time?
```

**Apply feedback to:**
- Refine tracer bullet
- Adjust next slice's approach
- Update documentation if assumptions were wrong
- Identify new issues that weren't obvious before

**Example:**
```
Completed: "User can enter email in registration form"
Feedback: "Form validation is much simpler if email is optional at first"
Applied to: "User can add email to profile later" (next slice)
New issues: "Add email validation to profile update form"
```

### 2. Testing Feedback

**From tests during and after implementation:**

```
Testing reveals:
- Implementation doesn't match test expectations
- Tests catch bugs before review
- Some requirements were unclear
- Edge cases the requirements didn't mention
```

**Apply feedback to:**
- Fix implementation immediately
- Clarify requirements for next slice
- Update acceptance criteria
- Add test examples for common mistakes

**Example:**
```
Test failed: "User cannot register with duplicate email"
Revealed: Requirements didn't specify what error message to show
Applied to: "Add friendly error message for duplicate email" (new issue)
```

### 3. Code Review Feedback

**From human or AI review:**

```
Review reveals:
- Code doesn't follow established patterns
- Better approaches exist
- Missing error handling
- Performance concerns
- Security issues
```

**Apply feedback to:**
- Fix current implementation
- Document patterns for future work
- Update coding standards
- Create educational examples

**Example:**
```
Review comment: "This should use our QueryBuilder pattern like in AccountService"
Feedback: "Developer wasn't aware of the pattern"
Applied to: 
  - Update documentation with more examples
  - Link pattern documentation from places it's used
  - Share pattern in team review
```

### 4. QA/Testing Feedback

**From manual testing and QA:**

```
QA discovers:
- Features don't work as advertised
- Edge cases and error conditions
- Performance problems
- Usability issues
- Integration problems
```

**Apply feedback to:**
- Fix bugs and issues
- Create test cases for discovered issues
- Update documentation with real-world usage
- Create new issues for follow-up work

**Example:**
```
QA found: "Search returns results in 2 seconds on real data, too slow"
Performance requirement was specified but not achieved
Applied to: 
  - "Add database indexes for search performance" (new issue)
  - Test includes: "Search must complete in < 500ms"
```

### 5. Stakeholder Feedback

**From users, product managers, team leaders:**

```
Feedback reveals:
- Feature doesn't solve the actual problem
- Different priorities than expected
- Missing edge cases or scenarios
- Performance or UX expectations not met
```

**Apply feedback to:**
- Adjust next iteration priorities
- Create new issues for requested changes
- Update product requirements
- Schedule clarification discussions

**Example:**
```
User feedback: "I like the search, but I need to see creation date in results"
Applied to: "Show creation date in search results" (new issue)
Changed priority: This becomes higher priority than planned next feature
```

---

## How to Implement Feedback Loops

### Step 1: Establish Feedback Collection Points

Define when and how you collect feedback:

| Phase | Feedback Source | How to Collect |
|-------|-----------------|----------------|
| **Implementation** | Developer/AI observations | Commit messages, notes, questions |
| **Testing** | Test results, test failures | Test logs, coverage reports |
| **Review** | Code reviewers, architects | Review comments, discussion |
| **QA** | Manual testing, edge cases | Bug reports, test session notes |
| **Stakeholders** | Users, product, team | Conversations, surveys, metrics |

### Step 2: Make Feedback Actionable

Transform feedback into decisions:

```
Feedback: "This is slower than expected"
  ↓
Actionable: "Add indexes to [table] for [query]"
            "Profile code path [X] to find bottleneck"

Feedback: "This doesn't work the way I expected"
  ↓
Actionable: "Clarify documentation with example"
            "Change default behavior to [option]"

Feedback: "I found a scenario that breaks"
  ↓
Actionable: "Add test case for [scenario]"
            "Add handling for [edge case]"
```

### Step 3: Create Issues from Feedback

Document feedback as issues:

```
Title: Add database indexes for search performance
From: QA testing revealed search too slow
Original Issue: #15 (Add user search feature)
Priority: High (blocks performance acceptance)
Acceptance Criteria:
  - Search completes in < 500ms on 1M records
  - Indexes are created in migration

Labels: performance, database, follow-up
```

### Step 4: Link Feedback to Original Work

Connect feedback back to where it came from:

```
Original Issue: #15 "Add user search"
  Feedback Issues:
    - #23 "Add database indexes for search performance"
    - #24 "Show creation date in search results"
    - #25 "Add search result count display"
  Lessons Learned:
    - Email pattern should have been indexed upfront
    - Users want to see more metadata in results
    - Should have done performance testing earlier
```

### Step 5: Apply Learning to Next Work

Use feedback to improve:

```
Next feature: "Add email search"
Lessons from previous feature:
  ✓ Create indexes upfront (not after performance testing)
  ✓ Test on realistic data volume early
  ✓ Ask stakeholders what metadata they want to see
  ✓ Plan for edge cases like duplicate emails
```

---

## Feedback Loop Speed

Speed matters: faster feedback → faster learning → faster improvement.

### Slow Feedback Loops (weeks)
```
Build feature (1 week)
  ↓
Ship to production (1 week)
  ↓
Users discover issues
  ↓
Learn and iterate
Result: Issues don't get fixed for weeks
```

### Fast Feedback Loops (hours)
```
Build one slice (2 hours)
  ↓
Test it (30 minutes)
  ↓
Review it (30 minutes)
  ↓
Learn from it (15 minutes)
  ↓
Build next slice with improvements (2 hours)
Result: Issues get fixed in the next slice
```

**Vertical slicing and tracer bullets enable fast feedback loops:**
- Complete slice in hours → feedback in hours
- Each slice tests learning from previous slice
- Issues are caught and fixed before heavy investment

---

## Feedback Loop Patterns

### The Refinement Loop
```
Tracer Bullet v1 (minimal, working)
   ↓ Feedback: "Works but needs better error handling"
Refinement Pass 1 (add error handling)
   ↓ Feedback: "Fast but slow on large data"
Refinement Pass 2 (add performance optimization)
   ↓ Feedback: "Good but API is confusing"
Refinement Pass 3 (simplify API)
```

Each pass is a feedback loop improving one aspect.

### The Discovery Loop
```
Initial Design (our best guess)
   ↓ Feedback: "Doesn't match user workflow"
Revised Design (based on feedback)
   ↓ Feedback: "Getting closer but [detail] is still wrong"
Final Design (validated with users)
```

### The Quality Loop
```
Implementation (written to pass tests)
   ↓ Code Review (feedback on quality)
Revision (apply review feedback)
   ↓ Testing (feedback from QA)
Fixes (apply QA feedback)
   ↓ Ready for Deployment
```

---

## Feedback Loops with AI Agents

Feedback loops are essential when working with AI:

### How AI Uses Feedback

1. **Initial Attempt:** AI implements based on requirements
2. **Feedback:** Tests fail, or review shows issues
3. **Adjustment:** AI reads feedback and revises implementation
4. **Iteration:** Repeat until feedback is positive

### Feedback That Helps AI

Good feedback for AI is:
- **Specific** — "Function name should be `searchUsers` not `findUsers`"
- **Actionable** — "Add validation for empty email"
- **Contextual** — "This pattern is used in ServiceX at line 42"
- **Clear** — "Tests show this returns wrong data type"

Bad feedback for AI is:
- **Vague** — "This isn't right"
- **Implicit** — Assuming AI knows unstated requirements
- **Historical** — "We did it differently before" (if that's old/wrong)

---

## Common Pitfalls

### ❌ Ignoring Feedback

**Problem:** Feedback is collected but not acted on.

**Impact:**
- Same issues repeat
- No improvement cycle
- Team gets frustrated

**Fix:** Treat feedback as actionable information:
- Create issues for feedback
- Prioritize based on impact
- Close the loop by implementing improvements

### ❌ Slow Feedback Cycles

**Problem:** Feedback takes weeks to collect and act on.

**Impact:**
- Issues aren't caught until late
- Mistakes compound
- High cost of correction

**Fix:** Accelerate feedback collection:
- Test after each slice (hours, not weeks)
- Review during implementation (day, not after)
- Integrate frequently (catch issues early)

### ❌ Feedback Without Context

**Problem:** Feedback is collected but context of why is lost.

**Example (wrong):**
```
"Search is too slow"
(Why? What's the threshold? What data? What's the impact?)
```

**Fix:** Collect feedback with context:
```
"Search takes 2 seconds on 1M records; requirement is 500ms; this blocks feature release"
```

### ❌ No Mechanism to Apply Feedback

**Problem:** Feedback is collected but there's no clear process to act on it.

**Impact:**
- Feedback accumulates but nothing changes
- Team feels unheard
- Improvements don't materialize

**Fix:** Build feedback into planning:
- Review feedback before planning next work
- Create issues from feedback
- Prioritize based on impact
- Track improvements

---

## Benefits

- **Continuous Improvement:** Each iteration is better than the last
- **Early Issue Detection:** Problems are caught and fixed quickly
- **Reduced Rework:** Issues don't compound over time
- **Learning:** Team learns from each piece of work
- **Alignment:** Work stays aligned with actual needs
- **Quality:** Feedback ensures high standards
- **Team Development:** Feedback helps everyone improve

---

## Related Patterns

- **[Vertical Slicing](vertical-slicing.md)** — Each slice is a feedback opportunity
- **[Tracer Bullets](tracer-bullets.md)** — Tracer bullets are refined based on feedback
- **[Test-Driven Development](test-driven-development.md)** — Tests provide rapid feedback
- **[Context Management](context-management.md)** — Feedback improves context for next work

---

## The Feedback Loop in the Software Development Process

Feedback loops appear throughout the [Software Development Process](../processes/software-development.md):

- **Phase 1 (Discovery):** Feedback from stakeholders refines understanding
- **Phase 2 (Design):** Feedback from design review shapes final design
- **Phase 3 (Decomposition):** Feedback from early slices influences later slices
- **Phase 4 (Implementation):** Test feedback drives refinement within each slice
- **Phase 5 (Validation):** Review and QA feedback guides corrections
- **Phase 6 (Resolution):** Team feedback shapes follow-up issues

---

## References

- "Continuous Delivery" by Jez Humble and David Farley
- "The Lean Startup" by Eric Ries (feedback loops and iteration)
- Agile development principles (embrace change through feedback)
- DevOps practices (continuous feedback and deployment)

---

**Last Updated:** 2026-08-16  
**Status:** Core Pattern Documentation (MVP)
