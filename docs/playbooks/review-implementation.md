# Playbook: Review Implementation

## Overview

This playbook provides a systematic approach to reviewing code implementations. Whether you're reviewing work by a human developer, an AI agent, or both, this playbook helps ensure code meets quality standards, architectural patterns, and acceptance criteria before merging.

## When to Use This Playbook

- You're reviewing a pull request from a team member or AI agent
- You're preparing a code review in Phase 5 (Validation) of the [Software Development Process](../processes/software-development.md#phase-5-validation)
- You need to verify acceptance criteria are met before merging
- You're doing a fresh-context review (reviewing code you didn't write)
- You want to identify bugs, regressions, risks, and missing tests
- You're reviewing implementation against a PRD

## Key Principle: Review Priorities

**Focus your review time where it matters most:**
1. **Bugs and Regressions** (Critical) - Does this code break existing functionality? Are there obvious bugs?
2. **Risks and Vulnerabilities** (Critical) - Does this code introduce security or performance risks?
3. **Missing Tests** (High) - Is new code adequately tested? Are edge cases covered?
4. **Design and Architecture** (Medium) - Does this follow patterns? Is it maintainable?
5. **Code Style and Polish** (Low) - Could style issues be caught by linters?

See [TDD Pattern](../patterns/test-driven-development.md) for test strategy.

---

## Phase 1: Preparation (10-15 minutes)

### 1.1 Gather Context

**Objective:** Understand what this PR is trying to do before reading code.

**Steps:**
1. Read the PR title and description
2. Review the linked issue(s) - what are the acceptance criteria?
3. Check for related PRs or issues
4. Note any special implementation guidance
5. Identify the reviewer checklist (if any)

**What to capture:**
```
## PR Context

**Title:** [PR title]
**Issue:** [Link to issue(s)]
**Acceptance Criteria:**
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

**Key Areas to Review:**
- [Most risky component]
- [Most complex component]
- [Most changed component]

**Implementation Notes:**
- [Any special approach taken]
- [Any patterns applied]
```

**Example:**

```
## PR Context

**Title:** Add Decision Point Marker Parser (Issue #101)
**Issue:** #101 - Parse decision markers from requirements

**Acceptance Criteria:**
- [ ] Parser recognizes @decision markers in text
- [ ] Parser extracts marker position, context, and options
- [ ] DecisionCheckpoint objects created correctly
- [ ] All parsing logic has unit tests (100% coverage)
- [ ] Example requirements file with markers provided

**Key Areas to Review:**
- Parser regex/parsing logic (most complex)
- DecisionCheckpoint model (data structure)
- Test coverage (all parsing paths)
- Error handling for malformed markers

**Implementation Notes:**
- Applied TDD pattern: tests written first
- Used context management pattern for marker extraction
```

### 1.2 Set Up Your Review Environment

**Objective:** Prepare to review code efficiently.

**Steps:**
1. Check out the branch locally (if possible)
2. Run the test suite - do all tests pass?
3. Run the build - does it compile/build without errors?
4. Review the file diff - which files changed?
5. Prioritize your review - which files are most critical?

**Pre-Review Checklist:**

```
[ ] Branch checked out locally
[ ] `npm test` (or equivalent) passes
[ ] `npm run build` (or equivalent) succeeds
[ ] No obvious errors or warnings from linter
[ ] File diff reviewed (which files changed?)
[ ] Critical files identified
[ ] Test files reviewed (tests written?)
```

---

## Phase 2: Bug and Risk Assessment (15-25 minutes)

### 2.1 Look for Obvious Bugs

**Objective:** Identify critical correctness issues.

**Steps:**
1. Scan each changed file for syntax errors or typos
2. Look for off-by-one errors, null pointer dereferences, type mismatches
3. Check for logic errors: Do conditionals make sense? Are edge cases handled?
4. Look for unfinished code: TODOs, console.logs, commented-out code
5. Check for resource leaks: Are connections/files properly closed?
6. Verify error handling: Are errors caught and logged?

**Bug Hunting Checklist:**

```
## Critical Bugs

[ ] No syntax errors
[ ] No obvious null/undefined dereference
[ ] No type mismatches (types line up correctly)
[ ] Logic is sound (conditionals make sense)
[ ] No off-by-one errors in loops/arrays
[ ] No unfinished code (TODOs, console.logs, commented code)
[ ] Resources properly closed/cleaned up
[ ] Error cases handled gracefully
[ ] No infinite loops or deadlocks (threading code)
[ ] No hardcoded values that should be configurable
```

**Example Bug Hunt:**

```
## Code Review: Decision Point Parser

### Potential Issues Found:
1. Line 45: `markers.length > index` should be `markers.length > index + 1`
   - This could cause off-by-one error in marker extraction
   - SEVERITY: High (logic error)
   - RECOMMENDATION: Fix and add test case for boundary condition

2. Line 67: `throw new Error("...")` without error type
   - Should throw DecisionParsingError for consistency
   - SEVERITY: Low (inconsistency with error handling pattern)
   - RECOMMENDATION: Use consistent error type

3. Line 12: Missing null check on options parameter
   - If options is null, line 15 will throw
   - SEVERITY: Medium (edge case)
   - RECOMMENDATION: Add null check or validate in constructor
```

### 2.2 Identify Risks and Vulnerabilities

**Objective:** Spot security, performance, and reliability risks.

**Steps:**
1. **Security:** Any new inputs from users? Are they validated? Could this be exploited?
2. **Performance:** Any O(n²) loops? Database queries in loops? Unbounded allocations?
3. **Concurrency:** Any race conditions? Shared state? Thread safety?
4. **Dependencies:** Any new external dependencies? Any outdated versions?
5. **Backwards Compatibility:** Does this break existing code or APIs?

**Risk Assessment Checklist:**

```
## Risks and Vulnerabilities

### Security
[ ] All user inputs validated
[ ] No SQL injection vectors
[ ] No XSS vectors
[ ] Sensitive data not logged or exposed
[ ] Authentication/authorization correct

### Performance
[ ] No obvious O(n²) algorithms
[ ] No database queries in loops
[ ] No unbounded memory allocations
[ ] Response times acceptable

### Reliability
[ ] No race conditions
[ ] No deadlocks
[ ] Proper error recovery
[ ] Graceful degradation

### Compatibility
[ ] No breaking API changes
[ ] Backwards compatible with previous versions
[ ] No dependency version conflicts
```

**Example Risk Assessment:**

```
## Risk Assessment: Decision Point Parser

### Security
[x] All inputs validated (markers validated against pattern)
[x] No injection vectors (processing plain text, not SQL)
[x] Error messages don't leak sensitive info

### Performance
[x] O(n) algorithm (single pass through text)
[x] No loops within loops
[x] Memory usage bounded by input size

### Compatibility
[x] No API changes
[x] New class doesn't modify existing classes
[x] No dependency changes
```

---

## Phase 3: Test Coverage Assessment (15-20 minutes)

### 3.1 Verify Tests Exist and Are Good

**Objective:** Ensure new code is adequately tested.

**Steps:**
1. Look at test files - are they well-organized?
2. For each new function/class, is there at least one test?
3. Are edge cases and error conditions tested?
4. Do tests actually verify behavior (not just that code runs)?
5. Is test coverage high (>80% for critical code)?

**Test Quality Checklist:**

```
## Test Coverage

[ ] Tests exist for all new public APIs
[ ] Edge cases are tested (empty input, null, boundary values)
[ ] Error conditions are tested (invalid input, failures)
[ ] Tests are isolated (one test, one behavior)
[ ] Tests are clear: Arrange, Act, Assert pattern
[ ] Tests don't test implementation details
[ ] Test coverage > 80% for new code
[ ] All tests pass
[ ] Tests are appropriately fast (< 100ms per test)
```

**What Good Tests Look Like:**

```javascript
// Good: Clear test structure, tests one behavior
test('should extract marker from simple @decision annotation', () => {
  // Arrange
  const text = 'Before @decision(frontend-vs-backend) After';
  const parser = new DecisionMarkerParser();
  
  // Act
  const markers = parser.parse(text);
  
  // Assert
  expect(markers).toHaveLength(1);
  expect(markers[0].type).toBe('decision');
  expect(markers[0].options).toContain('frontend-vs-backend');
  expect(markers[0].position).toBe(7); // Position of @decision
});

// Good: Tests error case
test('should throw DecisionParsingError on invalid marker', () => {
  const text = 'Invalid @decision(';
  const parser = new DecisionMarkerParser();
  
  expect(() => parser.parse(text)).toThrow(DecisionParsingError);
});

// Good: Tests edge case
test('should handle empty options in marker', () => {
  const text = '@decision()';
  const parser = new DecisionMarkerParser();
  
  const markers = parser.parse(text);
  expect(markers[0].options).toHaveLength(0);
});
```

**What Bad Tests Look Like:**

```javascript
// Bad: Tests implementation, not behavior
test('should call extractOptions method', () => {
  const parser = new DecisionMarkerParser();
  spyOn(parser, 'extractOptions');
  parser.parse('@decision(opt1, opt2)');
  expect(parser.extractOptions).toHaveBeenCalled();
});

// Bad: No assertion, just runs code
test('should parse text', () => {
  const parser = new DecisionMarkerParser();
  const markers = parser.parse('@decision(foo)');
  // Missing assertion!
});

// Bad: Tests multiple things
test('should parse and validate and save markers', () => {
  const parser = new DecisionMarkerParser();
  const db = new MockDatabase();
  const markers = parser.parse('@decision(x)');
  expect(markers).toBeDefined();
  expect(db.save).toHaveBeenCalled();
  // This test tests too many things!
});
```

### 3.2 Identify Missing Tests

**Objective:** Flag gaps in test coverage.

**Steps:**
1. For each new function/class, identify:
   - Happy path (basic usage)
   - Error cases (what can go wrong?)
   - Edge cases (boundary conditions, empty inputs, etc.)
2. Check which ones are tested
3. Flag missing coverage

**Missing Test Assessment:**

```
## Missing Tests

### New Function: parseDecisionMarker()

Happy Path:
- [x] Parse marker with single option
- [x] Parse marker with multiple options
- [x] Extract marker position

Error Cases:
- [x] Malformed marker syntax
- [x] Missing option specification
- [ ] MISSING: Empty options (should it error or return empty array?)

Edge Cases:
- [ ] MISSING: Very long option names (> 500 chars)
- [ ] MISSING: Special characters in options (quotes, slashes)
- [ ] MISSING: Multiple markers on same line

### Action Items:
1. Add test for empty options
2. Add test for special characters
3. Add test for multiple markers on same line
```

---

## Phase 4: Architecture and Design Review (15-25 minutes)

### 4.1 Verify Pattern Adherence

**Objective:** Ensure code follows established patterns.

**Steps:**
1. Review the issue/PR description - which patterns should be applied?
2. Look at the code - does it follow those patterns?
3. Check against [Vertical Slicing](../patterns/vertical-slicing.md): Is this end-to-end? Does it touch all layers?
4. Check against [TDD](../patterns/test-driven-development.md): Is it well-tested?
5. Check against [Context Management](../patterns/context-management.md): Is information flow clear?
6. Check project conventions - naming, file organization, etc.

**Pattern Adherence Checklist:**

```
## Architecture and Design

### Patterns Applied (from issue)
- [x] Vertical Slicing: This is an end-to-end slice
- [x] TDD: Tests written, code follows tests
- [x] Context Management: Clear input/output, no hidden state

### Code Organization
[ ] Files organized logically
[ ] No circular dependencies
[ ] Clear module boundaries
[ ] Follows project conventions (naming, structure)

### Maintainability
[ ] Code is readable (clear variable names, functions do one thing)
[ ] Complex logic is commented
[ ] Functions are reasonable size (< 30 lines preferred)
[ ] No duplicated code

### Design Quality
[ ] No tight coupling
[ ] Abstractions are appropriate
[ ] Error types match project conventions
[ ] Configuration is externalized (not hardcoded)
```

**Example Pattern Review:**

```
## Architecture Review: Decision Point Parser

### Vertical Slicing
- [x] Parser is end-to-end: takes text input, produces checkpoint objects
- [x] Works independently: doesn't require agent or database yet
- [x] Can be tested in isolation
- [x] Fits cleanly into larger system

### TDD Pattern
- [x] Tests written first (test file included)
- [x] Tests drive implementation
- [x] Happy path tested
- [x] Error cases tested
- Coverage: 87% (good for first slice)

### Code Quality
- [x] DecisionMarkerParser class is focused and simple
- [x] Method names clear (parse, extract, validate)
- [x] Error handling explicit
- [x] No global state or side effects

### Issues Found
1. Possible improvement: Extract regex to constant instead of inlining
   - SEVERITY: Low (readability)
   - RECOMMENDATION: Add named constant for pattern
```

### 4.2 Check for Architectural Debt

**Objective:** Identify decisions that might create problems later.

**Steps:**
1. Look for quick hacks or temporary workarounds
2. Look for hardcoded values that should be configurable
3. Look for insufficient abstraction (too specific to current use case)
4. Look for scaling concerns (will this work at 10x load?)
5. Look for technical debt indicators (marked TODOs, unsupported configurations)

**Architectural Debt Checklist:**

```
## Technical Debt Assessment

[ ] No quick hacks or temporary workarounds (or if present, tracked in issue)
[ ] No hardcoded values (configuration is externalized)
[ ] Sufficient abstraction (not too specific to current use case)
[ ] Scaling concerns addressed (or noted for later)
[ ] No marked TODOs blocking merge
[ ] No performance concerns that will make future work difficult
```

---

## Phase 5: Functional Review Against Acceptance Criteria (10-15 minutes)

### 5.1 Verify Acceptance Criteria

**Objective:** Ensure the code actually delivers what was asked for.

**Steps:**
1. Review the acceptance criteria from the issue
2. For each criterion, verify the code satisfies it
3. Test locally if possible
4. Check related functionality (did this break anything?)

**Acceptance Verification:**

```
## Acceptance Criteria Verification

From Issue #101:
- [ ] Parser recognizes @decision markers in text
  → Code: `parseDecisionMarker()` method, regex pattern
  → Test: `test-parser.ts` line 45-60
  → VERIFIED ✓

- [ ] Parser extracts marker position, context, and options
  → Code: `extractMarkerInfo()` method
  → Test: Multiple tests verify position, options extraction
  → VERIFIED ✓

- [ ] DecisionCheckpoint objects created correctly
  → Code: `DecisionCheckpoint` class constructor
  → Test: `test-checkpoint.ts` verifies properties
  → VERIFIED ✓

- [ ] All parsing logic has unit tests (100% coverage)
  → Coverage report: 87% (Close, but not 100%)
  → ISSUE: Missing tests for edge cases
  → ACTION: Request additional edge case tests before merge
  
- [ ] Example requirements file with markers provided
  → File: `examples/example-requirements.md`
  → Content: Contains multiple @decision markers
  → VERIFIED ✓
```

### 5.2 Test Locally (If Possible)

**Objective:** Verify the code actually works.

**Steps:**
1. Check out the PR locally
2. Run the build: `npm run build`
3. Run the tests: `npm test`
4. Try using the feature: `npm run dev`
5. Manually test with the provided example

**Manual Test Checklist:**

```
## Manual Testing

[ ] Build succeeds without errors
[ ] All tests pass
[ ] No console errors or warnings
[ ] Feature works as described in PR
[ ] Example from PR description works
[ ] No obvious performance issues
[ ] No crashes or exceptions
```

---

## Phase 6: Final Assessment and Recommendations (10-15 minutes)

### 6.1 Summarize Findings

**Objective:** Create a clear summary of your review.

**Steps:**
1. List any issues found (blocking vs. non-blocking)
2. List any questions or requests for clarification
3. Note positive observations (good testing, clean code, etc.)
4. Determine: Approve, Request Changes, or Comment

**Review Summary Template:**

```
## Code Review Summary

**Overall Assessment:** [Approve / Request Changes / Comment]

**Blocker Issues** (Must Fix Before Merge):
1. [Critical bug or missing requirement]
   - Line/file: [Location]
   - Impact: [Why this is critical]
   - Suggestion: [How to fix]

2. [Missing tests or acceptance criterion not met]
   - Missing: [What's missing]
   - Impact: [Why this matters]
   - Suggestion: [How to address]

**Minor Issues** (Should Fix, But Not Blocking):
1. [Style issue, inconsistency, or improvement suggestion]
   - Location: [Where]
   - Why: [Why this matters]
   - Suggestion: [How to improve]

**Questions**:
- [Clarification request]
- [Design decision question]

**Positive Observations**:
- [What went well]
- [Good practices observed]

**Recommendation**:
- [ ] Approve as-is
- [ ] Request changes on [items]
- [ ] Approve after minor revisions
```

**Example Review Summary:**

```
## Code Review Summary: Issue #101

**Overall Assessment:** Request Changes (minor issues)

**Blocker Issues**: None identified

**Minor Issues**:
1. Test coverage at 87%, but criterion requires 100%
   - File: `src/parser.ts`
   - Missing coverage: Edge cases (special characters, very long options)
   - Suggestion: Add tests for boundary conditions

2. Hardcoded regex pattern
   - Line: `src/parser.ts` line 12
   - Improvement: Extract to named constant for clarity
   - Suggestion: `const DECISION_MARKER_PATTERN = /...`

**Questions**:
- How should empty options be handled? Error or empty array?
- Are there performance requirements for very large files?

**Positive Observations**:
- Excellent test structure (Arrange-Act-Assert pattern)
- Clear error handling with specific error types
- Good inline comments explaining complex logic
- Follows project conventions well

**Recommendation**:
- Add tests for edge cases (special characters, long options)
- Extract regex to constant
- Then approve for merge
```

### 6.2 Provide Actionable Feedback

**Objective:** Make it easy for the implementer to fix issues.

**Steps:**
1. For each issue, provide: Problem + Why it matters + How to fix
2. Link to relevant documentation or examples
3. Suggest specific changes, not vague criticism
4. Be kind and constructive

**Good vs. Bad Feedback:**

```
BAD FEEDBACK:
"This regex is terrible. You need to fix it."

GOOD FEEDBACK:
"The regex on line 12 could be made more readable by extracting it to a named constant.
This would make it easier to understand what patterns are supported and to reuse in other
places. Example: const MARKER_PATTERN = /...; See project conventions in STYLE_GUIDE.md"
```

---

## Phase 7: Guide Approval Process (5-10 minutes)

### 7.1 Track Changes

**Objective:** Ensure requested changes are made and verified.

**Steps:**
1. If "Request Changes": Be clear about what must be fixed vs. what's optional
2. After implementer makes changes: Re-review relevant sections
3. Use GitHub review threads to track conversation
4. Don't approve until all blockers are addressed

### 7.2 Approve for Merge

**Objective:** Make the final call on whether to merge.

**When to Approve:**
- ✓ No blocker issues
- ✓ All acceptance criteria met
- ✓ Adequate test coverage (> 80%)
- ✓ No obvious bugs or risks
- ✓ Follows patterns and conventions
- ✓ Code is maintainable

**When to Request Changes:**
- Changes needed for requirements, bugs, or risks
- Critical test coverage gap
- Architecture or design concerns

**When to Comment:**
- Minor suggestions (not blocking)
- Questions or discussion points
- Suggestions for future work

---

## For AI Agents

### What This Playbook Tells You

1. **How to Review Systematically:** Start with context, then bugs/risks, then tests, then design
2. **What to Focus On:** Bugs and risks first; style last
3. **How to Give Feedback:** Be specific; provide solutions, not just problems
4. **When Something Is Done:** All acceptance criteria met, adequate tests, no blockers

### How to Respond to Code Reviews

- If reviewer requests changes: Fix and re-request review
- If reviewer asks questions: Answer specifically and update code if needed
- If reviewer approves: Code is ready to merge
- If you disagree with feedback: Discuss in review thread respectfully

---

## Integration with Software Development Process

This playbook supports **Phase 5 (Validation)** of the [Software Development Process](../processes/software-development.md#phase-5-validation), specifically the "Implementation Review" and "Fresh-Context Review" steps.

---

## Common Pitfalls

| Pitfall | What Goes Wrong | Solution |
|---------|-----------------|----------|
| **Reviewing Style First** | Spend time on formatting, miss critical bugs | Focus on bugs/risks/tests first; leave style to linters |
| **Vague Feedback** | Implementer doesn't know how to fix issues | Be specific: show the problem, explain why it matters, suggest solution |
| **Nitpicking Everything** | Demoralizes implementer; slows down work | Distinguish blockers (must fix) from nice-to-haves (can defer) |
| **Skipping Tests** | Code passes review but fails in production | Always verify test coverage; check tests actually test behavior |
| **Not Running Code** | Miss obvious runtime errors | Run build and tests locally before approving |
| **Approving Unclear Code** | Future maintainers struggle | Request clarification or documentation if code is unclear |
| **Ignoring Acceptance Criteria** | Code doesn't actually meet requirements | Always verify each acceptance criterion before approving |

---

## Related Patterns and Playbooks

- **Pattern:** [TDD](../patterns/test-driven-development.md) — Understand what good test coverage looks like
- **Pattern:** [Vertical Slicing](../patterns/vertical-slicing.md) — Understand what end-to-end slice means
- **Pattern:** [Feedback Loops](../patterns/feedback-loops.md) — Code review is a feedback loop
- **Process:** [Software Development Process - Phase 5 (Validation)](../processes/software-development.md#phase-5-validation) — Where code review fits
- **Playbook:** [Investigate Bug](investigate-bug.md) — When code review finds bugs

---

## References

- Code Review Best Practices: [Google Code Review Standards](https://google.github.io/eng-practices/review/)
- TDD and Test Quality: [TDD Pattern](../patterns/test-driven-development.md)
- Architecture and Design: [Software Development Process](../processes/software-development.md)
