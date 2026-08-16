# Playbook: Investigate a Bug

## Overview

This playbook provides a systematic approach to investigating bugs in existing code. Whether you're tracking down a production issue, debugging test failures, or investigating unexpected behavior, this playbook helps you narrow down the root cause efficiently and avoid common debugging traps.

## When to Use This Playbook

- A test is failing and you need to understand why
- A bug has been reported and you need to investigate
- Code that used to work is now broken (regression)
- You're experiencing unexpected behavior you don't understand
- You're trying to understand why a feature isn't working as intended
- You're diagnosing a performance or reliability problem
- You're in Phase 5 (Validation) or Phase 6 (Resolution) of the [Software Development Process](../processes/software-development.md)

## Key Principle: Narrow the Search Space

Debugging is about finding the one broken thing among thousands of lines of code. The faster you narrow down where the bug is, the faster you fix it. This playbook helps you systematically eliminate possibilities.

---

## Phase 1: Reproduce the Bug (15-30 minutes)

### 1.1 Understand the Symptom

**Objective:** Define exactly what's going wrong.

**Steps:**
1. Document what you observe: What's happening that shouldn't?
2. Document expected behavior: What should happen?
3. Note when it happens: Always? Intermittently? Under specific conditions?
4. Note who reported it: User, test, monitoring system? (They might have more context)
5. Check for related issues: Has this been reported before?

**Bug Report Template:**

```
## Bug Report

**Title:** [Clear, specific description of the problem]

**Expected Behavior:**
When [user does X], the system should [do Y]

**Actual Behavior:**
Instead, the system [does Z]

**How to Reproduce:**
1. [Step 1]
2. [Step 2]
3. [Step 3]
Result: [Observed symptom]

**Frequency:**
- [ ] Always happens (reproducible)
- [ ] Sometimes happens (intermittent)
- [ ] Happens under specific conditions (describe)

**Environment:**
- OS: [Windows / macOS / Linux]
- Node version: [if applicable]
- Dependencies: [Any unusual setup?]

**Error Messages:**
[Any error messages, stack traces, console output]

**Related Issues:**
[Link to any related bugs or discussions]
```

**Example Bug Report:**

```
## Bug Report: Decision Point Parser Fails on Special Characters

**Expected Behavior:**
Parser should handle special characters in option names. When I write:
"@decision(validate-data, validate 'data', validate@data)"
The parser should extract all three options.

**Actual Behavior:**
Parser throws DecisionParsingError when it encounters quotes or '@' symbols

**How to Reproduce:**
1. Create file with marker: @decision(option-with-@-symbol)
2. Run parser
3. Parser throws error

**Frequency:**
Always happens with special characters

**Error:**
DecisionParsingError: Invalid character in option at position 42
Stack trace: ...

**Environment:**
- OS: Windows
- Node: 18.12.0
```

### 1.2 Create a Minimal Reproduction

**Objective:** Find the smallest possible way to trigger the bug.

**Steps:**
1. Start with the reported reproduction steps
2. Simplify: Remove anything that's not essential
3. Create a minimal test case (code or input that reproduces the bug)
4. Verify you can reproduce it consistently
5. Document the minimal reproduction

**Minimal Reproduction Example:**

```
## Minimal Reproduction

**Code that triggers the bug:**
```javascript
const parser = new DecisionMarkerParser();
const text = '@decision(option@1)';
parser.parse(text); // Throws: DecisionParsingError
```

**This reproduces the bug with minimal code:**
- Don't need file I/O
- Don't need full application context
- Don't need database or network
- Just: input → parser → error

**Benefits of minimal reproduction:**
- Faster to test fixes
- Easier to pinpoint root cause
- Can be used as test case later
```

### 1.3 Collect Diagnostic Information

**Objective:** Gather data that will help you diagnose the problem.

**Steps:**
1. Run with debugging enabled (if possible)
2. Enable logging at DEBUG level
3. Capture relevant state:
   - Input data
   - System configuration
   - Recent changes (git log)
   - Performance metrics (if relevant)
4. Note what you've already tried

**Diagnostic Information Checklist:**

```
## Diagnostic Information

### Error Details
- [ ] Full error message captured
- [ ] Stack trace captured
- [ ] Error location identified (file, line)

### Environment
- [ ] System information (OS, version)
- [ ] Application version or commit
- [ ] Dependency versions
- [ ] Configuration values

### Reproduction
- [ ] Minimal reproduction created
- [ ] Can reproduce consistently?
- [ ] How many times to trigger? (always, 1 in 100?)

### Context
- [ ] What changed recently? (git log)
- [ ] Does it work on other machines/environments?
- [ ] Does it work with previous version?

### Performance (if performance bug)
- [ ] Baseline: How fast should it be?
- [ ] Current: How fast is it?
- [ ] Resource usage: CPU? Memory? Database?
```

---

## Phase 2: Narrow the Search Space (20-40 minutes)

### 2.1 Identify the Affected Layer

**Objective:** Is the bug in frontend, backend, database, external service, etc.?

**Steps:**
1. Trace the error back: Where does the error originate?
2. Check each layer:
   - **Frontend:** User sees wrong output
   - **API/Backend:** API returns wrong data or error
   - **Database:** Data is wrong in database
   - **External:** External service is down or returning wrong data
3. Look at the stack trace: Where is the error thrown?
4. Add logging: Print values at key points in the flow

**Layer Diagnosis Checklist:**

```
## Layer Diagnosis

### Is the issue in the Frontend?
- [ ] User interaction correct? (did user do what they intended?)
- [ ] Data displayed correctly? (is backend data being shown correctly?)
- [ ] Event handlers firing? (is click/change event captured?)
- [ ] State management working? (is state updated correctly?)

### Is the issue in the API/Backend?
- [ ] API endpoint being called? (add logging at endpoint)
- [ ] Input data correct? (log the request parameters)
- [ ] Business logic running? (add logging in key functions)
- [ ] Correct response returned? (log response before sending)

### Is the issue in the Database?
- [ ] Query executing? (log the SQL/query)
- [ ] Data in database correct? (query database directly)
- [ ] Migration applied? (check schema)
- [ ] Constraints violated? (check for constraint errors)

### Is the issue in External Services?
- [ ] External service up? (ping/check status)
- [ ] API keys valid? (check authentication)
- [ ] API version compatible? (check version)
- [ ] Rate limits hit? (check response headers)
```

**Example Layer Diagnosis:**

```
## Layer Diagnosis: Parser Throws on Special Characters

### Stack Trace Analysis:
```
Error: Invalid character in option at position 42
  at DecisionMarkerParser.validateOption (parser.ts:156)
  at DecisionMarkerParser.parse (parser.ts:89)
  at parseRequirements (index.ts:12)
```

### Diagnosis:
- Error thrown at: parser.ts line 156 (validateOption function)
- Layer: Backend/Core Logic (not database, not API, not external)
- Issue in: Input validation logic in parser

### Next Step:
Examine validateOption function to understand why special characters fail
```

### 2.2 Identify the Affected Code Path

**Objective:** Which code executes when the bug happens?

**Steps:**
1. Add logging at key decision points
2. Run the minimal reproduction
3. Look at logs: Which functions were called?
4. Which conditionals were taken?
5. Use a debugger if possible (set breakpoints)

**Code Path Tracing:**

```javascript
// Original code (no logging)
function parse(text) {
  const lines = text.split('\n');
  const markers = [];
  for (const line of lines) {
    const marker = extractMarker(line);
    if (marker) {
      markers.push(marker);
    }
  }
  return markers;
}

// With debug logging
function parse(text) {
  const lines = text.split('\n');
  const markers = [];
  console.log(`[DEBUG] Parsing ${lines.length} lines`);
  for (let i = 0; i < lines.length; i++) {
    const line = lines[i];
    console.log(`[DEBUG] Line ${i}: "${line}"`);
    const marker = extractMarker(line);
    console.log(`[DEBUG] Extracted marker:`, marker);
    if (marker) {
      markers.push(marker);
    }
  }
  return markers;
}

// Run with minimal reproduction
const result = parse('@decision(option@1)');
// [DEBUG] Parsing 1 lines
// [DEBUG] Line 0: "@decision(option@1)"
// [DEBUG] Extracted marker: { error: 'Invalid character' }
```

### 2.3 Identify Specific Function or Component at Fault

**Objective:** Narrow down to the specific function/method causing the issue.

**Steps:**
1. Look at the code path you traced
2. Look at the specific function where error occurs
3. Understand what the function is supposed to do
4. Understand what it's actually doing
5. Identify the mismatch

**Function Analysis:**

```
## Function: validateOption(option)

**Purpose:** Check if option name is valid

**Expected Behavior:**
- Accepts alphanumeric characters
- Accepts hyphens and underscores
- Rejects empty options
- Rejects special characters

**Actual Behavior:**
- Throws error on ANY special character, even @ and '
- No validation for empty options

**Code:**
```javascript
function validateOption(option) {
  const pattern = /^[a-z0-9_-]+$/i;  // Only alphanumeric, -, _
  if (!pattern.test(option)) {
    throw new DecisionParsingError(`Invalid character in option: ${option}`);
  }
}
```

**Analysis:**
- Regex pattern is too restrictive
- Pattern rejects @ and ' which might be valid
- OR: Requirements don't allow these characters

**Next Step:**
Clarify: Are @ and ' supposed to be allowed in option names?
```

---

## Phase 3: Root Cause Analysis (15-30 minutes)

### 3.1 Form a Hypothesis

**Objective:** Based on evidence, predict what's wrong.

**Steps:**
1. Review the code at the suspected location
2. Review the requirements (what should this code do?)
3. Identify the mismatch: Is the code wrong, or are the requirements wrong?
4. Form hypothesis: "The bug is because [specific reason]"
5. Write down your hypothesis and reasoning

**Hypothesis Template:**

```
## Root Cause Hypothesis

**Suspected Issue:**
[Specific code or configuration that's wrong]

**Evidence:**
- [Debug log showing incorrect value]
- [Code inspection showing incorrect logic]
- [Test result showing unexpected behavior]

**Root Cause:**
The code [does X] but it should [do Y] because [requirement states Z]

**How to Verify:**
- Test that would pass if fixed: [description]
- Change that would prove hypothesis: [proposed fix]
- Expected result after fix: [what should happen]
```

**Example Hypothesis:**

```
## Root Cause Hypothesis

**Suspected Issue:**
validateOption() function rejects @ and ' characters

**Evidence:**
- Error message: "Invalid character in option at @"
- Code inspection: Regex pattern /^[a-z0-9_-]+$/i excludes @ and '
- Debug log: Parser fails when parsing '@decision(option@1)'

**Root Cause:**
The regex pattern is too restrictive. It was written to allow only alphanumeric,
hyphens, and underscores. But the requirements document says options can include
special characters (for email-like references, file paths, etc.)

**How to Verify:**
1. Check requirements (PRD or issue): Do they allow special characters?
2. If yes: Change regex to allow more characters
3. Add test: @decision(option@1) should parse successfully
4. Re-run parser: Should work without error
```

### 3.2 Verify the Root Cause

**Objective:** Confirm your hypothesis is correct.

**Steps:**
1. Make the smallest possible change to fix the suspected issue
2. Run your minimal reproduction
3. Does it pass now?
4. If yes: Root cause confirmed
5. If no: Hypothesis was wrong; go back to Phase 2

**Root Cause Verification:**

```javascript
// Original (buggy) code:
const pattern = /^[a-z0-9_-]+$/i;

// Hypothesis: Pattern is too restrictive
// Test: Change pattern to allow more characters
const pattern = /^[a-z0-9_@\-'\.]+$/i;  // Now allows @, ', .

// Test with minimal reproduction:
const parser = new DecisionMarkerParser();
const result = parser.parse('@decision(option@1)');
console.log(result); // Does this work now?

// If it works: Hypothesis confirmed
// If it doesn't: Need different hypothesis
```

---

## Phase 4: Develop a Fix (15-30 minutes)

### 4.1 Identify All Instances of the Bug

**Objective:** Don't fix one instance and miss others.

**Steps:**
1. Search codebase for similar patterns
2. Is this bug in multiple places?
3. Does this same logic appear elsewhere?
4. Use grep or IDE search to find all instances

**Example Search:**

```bash
# Search for all validateOption calls
grep -rn "validateOption" src/

# Search for all similar regex patterns
grep -rn "pattern = /\^" src/

# Look for similar issues in codebase
grep -rn "Invalid character" src/
```

### 4.2 Develop a Comprehensive Fix

**Objective:** Fix the root cause properly, not just the symptom.

**Steps:**
1. Review the requirements: What should be allowed?
2. Decide on the solution:
   - Is this a code logic error?
   - Is this a configuration issue?
   - Is this a design issue?
3. Make the fix
4. Consider edge cases: Are there other scenarios this fix might affect?
5. Update documentation if needed

**Fix Development:**

```javascript
// Root Cause: Pattern too restrictive, doesn't match requirements

// Step 1: Review requirements
// PRD says: "Option names can include letters, numbers, hyphens, underscores,
// and special characters used in naming conventions (e.g., @, ., :)"

// Step 2: Develop fix
const VALID_OPTION_PATTERN = /^[a-zA-Z0-9_@\.\:\-]+$/;

function validateOption(option) {
  if (!option || option.trim().length === 0) {
    throw new DecisionParsingError('Option cannot be empty');
  }
  
  if (option.length > 100) {
    throw new DecisionParsingError('Option name too long (max 100 chars)');
  }
  
  if (!VALID_OPTION_PATTERN.test(option)) {
    throw new DecisionParsingError(
      `Invalid character in option: "${option}". ` +
      `Allowed: letters, numbers, -, _, @, ., :`
    );
  }
}

// Step 3: Test edge cases
// Should work:
// - option-name ✓
// - option_name ✓
// - option@1 ✓
// - user.email@domain ✓
// - namespace:option ✓

// Should fail:
// - '' (empty) ✓
// - 'a'.repeat(101) (too long) ✓
// - 'option!' (invalid char !) ✓
```

### 4.3 Write Tests for the Fix

**Objective:** Ensure the fix works and doesn't regress.

**Steps:**
1. Create a test that reproduces the bug
2. Verify test fails with original code
3. Apply your fix
4. Verify test passes with fixed code
5. Add tests for edge cases

**Test Writing (TDD approach):**

```javascript
describe('DecisionMarkerParser', () => {
  
  describe('validateOption', () => {
    const parser = new DecisionMarkerParser();
    
    // Test that reproduces the original bug
    test('should accept @ symbol in option names', () => {
      expect(() => {
        parser.validateOption('option@1');
      }).not.toThrow();
    });
    
    // Test for valid characters (per requirements)
    test('should accept valid special characters', () => {
      const validOptions = [
        'simple',
        'with-hyphen',
        'with_underscore',
        'with@symbol',
        'with.dot',
        'with:colon',
        'email@example.com',
      ];
      
      validOptions.forEach(option => {
        expect(() => parser.validateOption(option)).not.toThrow();
      });
    });
    
    // Test for invalid characters
    test('should reject invalid special characters', () => {
      const invalidOptions = [
        'with!exclamation',
        'with#hash',
        'with$dollar',
        'with%percent',
      ];
      
      invalidOptions.forEach(option => {
        expect(() => parser.validateOption(option)).toThrow(DecisionParsingError);
      });
    });
    
    // Test edge cases
    test('should reject empty option', () => {
      expect(() => parser.validateOption('')).toThrow();
    });
    
    test('should reject option longer than 100 chars', () => {
      const longOption = 'a'.repeat(101);
      expect(() => parser.validateOption(longOption)).toThrow();
    });
  });
});
```

---

## Phase 5: Verify the Fix (15-25 minutes)

### 5.1 Test the Fix Locally

**Objective:** Ensure the fix works before committing.

**Steps:**
1. Run your minimal reproduction: Does it pass now?
2. Run the full test suite: Do all tests pass?
3. Run any integration tests: Does the feature work end-to-end?
4. Manually test: Try the original reported scenario

**Fix Verification Checklist:**

```
## Fix Verification

[ ] Minimal reproduction passes
[ ] New test passes
[ ] All existing tests still pass (no regression)
[ ] Code builds without errors
[ ] No new linting warnings
[ ] Manual test of original scenario works
[ ] Performance impact acceptable (if performance bug)
```

### 5.2 Check for Related Issues

**Objective:** Make sure your fix doesn't break something else.

**Steps:**
1. Think about related functionality: What else uses this code?
2. Run broader test suite (not just unit tests)
3. Check for integration test failures
4. Look for performance regressions
5. Consider backwards compatibility: Does this break existing code?

**Regression Check:**

```bash
# Run all tests, not just the fixed one
npm test

# Check for performance regression
npm run benchmark

# Check for breaking changes
npm run check-api

# Look for related failures in CI/CD
# (Review build logs for warnings or failures)
```

---

## Phase 6: Document and Clean Up (10-15 minutes)

### 6.1 Document the Bug and Fix

**Objective:** Create a record so this doesn't happen again.

**Steps:**
1. Add commit message explaining:
   - What the bug was
   - Why it happened
   - How the fix addresses it
2. Link to related issues/PRs
3. Update any documentation that might be affected
4. Add a comment to the code if fix is non-obvious

**Good Commit Message:**

```
Fix: Parser rejects valid special characters in option names (#142)

The validateOption() regex pattern was too restrictive. According to the
PRD, option names can include letters, numbers, hyphens, underscores, and
special characters like @, ., : (used in email-like and namespaced references).

The pattern was:
  /^[a-z0-9_-]+$/i

Updated to:
  /^[a-zA-Z0-9_@\.\:\-]+$/

Also improved error messages to clearly show allowed characters.

Fixes: #142 (Parser fails on @decision(option@1))
Related: PR #143 (Add edge case tests)
```

### 6.2 Create a Test Case from This Bug

**Objective:** Prevent this bug from happening again.

**Steps:**
1. You already wrote a test that reproduces the bug
2. Keep that test in the codebase
3. Future developers will see: "This is important enough to test"
4. Future maintenance: This test prevents regression

**Test Preservation:**

```javascript
// In test-parser.ts - keep this test even after bug is fixed
// It documents: "This specific scenario was broken; don't break it again"

test('should accept @ symbol in option names (Regression: Issue #142)', () => {
  const parser = new DecisionMarkerParser();
  const result = parser.parse('@decision(option@1)');
  expect(result).toBeDefined();
  expect(result[0].options).toContain('option@1');
});
```

---

## Phase 7: Prevent Future Bugs (10-15 minutes)

### 7.1 Root Cause Analysis: Why Did This Slip Through?

**Objective:** Improve your process so similar bugs don't happen.

**Steps:**
1. This bug was in code for weeks/months before discovered
2. Why didn't tests catch it?
3. Why didn't code review catch it?
4. Why wasn't the edge case tested?

**Prevention Analysis:**

```
## Bug Prevention Analysis

**Bug:** Parser rejects @ in option names

**Why It Happened:**
1. Requirements weren't specific about allowed characters
2. Initial implementation was too restrictive
3. Test coverage didn't include special characters edge case
4. Code review didn't check against requirements document

**How to Prevent:**
1. Add explicit "allowed characters" to requirements document
2. Make test coverage requirements explicit (test all character types)
3. Code review checklist should include: "Are all requirements' examples tested?"
4. Add integration test that uses real-world examples (email@domain)

**Process Improvement:**
- Update test strategy to include character class testing
- Update code review checklist to verify edge cases
- Update requirements template to be explicit about character restrictions
```

### 7.2 Update Testing Strategy

**Objective:** Make sure similar bugs are caught by tests.

**Steps:**
1. Identify what test would have caught this bug
2. Make that type of test standard
3. Update project testing guidelines
4. Share learning with team

**Testing Improvement:**

```
## Updated Testing Approach

**For any validation function:**

### Test Matrix Approach
- [ ] Valid inputs: Test common valid cases
- [ ] Valid boundaries: Test edge cases of valid range
- [ ] Invalid inputs: Test common invalid cases
- [ ] Invalid boundaries: Test edge cases of invalid range
- [ ] Edge cases: Empty, null, very large, special chars, etc.

**Example for validateOption():**

Valid:
- Simple alphanumeric: 'option1'
- With separators: 'option-1', 'option_1'
- With special chars per spec: 'option@1', 'user.name', 'ns:option'

Invalid:
- Unpermitted special chars: 'option!', 'option#'
- Empty: ''
- Too long: 'a'.repeat(101)
- Whitespace: ' option'

### Updated Test Plan
When writing validation tests:
1. Explicitly test all character classes allowed
2. Explicitly test common disallowed characters
3. Test edge cases (empty, boundary length)
4. Test against real-world examples
```

---

## For AI Agents

### What This Playbook Tells You

1. **How to Debug Systematically:** Reproduce → Narrow → Root Cause → Fix → Verify
2. **How to Avoid Dead Ends:** Add logging; narrow search space; form hypotheses
3. **How to Avoid Regression:** Write tests that prevent repeat of same bug
4. **How to Fix Well:** Fix root cause, not symptoms; find all instances; test edge cases

### How to Report Bugs You Find

When your implementation uncovers a bug:
1. Create minimal reproduction
2. Document in GitHub issue with:
   - Expected behavior
   - Actual behavior
   - Minimal reproduction code
   - Stack trace (if any)
3. Flag for human review if blocking implementation

### Common Bugs AI Might Find

- Off-by-one errors in loops
- Type mismatches (passing wrong type to function)
- Missing null checks
- Regex too restrictive or too permissive
- Error handling that hides root cause
- Hardcoded values that should be configurable

---

## Integration with Software Development Process

This playbook supports:
- **Phase 5 (Validation):** Finding and reporting bugs during review
- **Phase 6 (Resolution):** Investigating and fixing bugs after discovered

---

## Common Pitfalls

| Pitfall | What Goes Wrong | Solution |
|---------|-----------------|----------|
| **Changing Too Much** | Fix one bug and introduce another | Make minimal changes; test after each change |
| **Not Reproducing** | Fix something that isn't actually broken | Always reproduce bug with minimal test case first |
| **Fixing Symptom** | Fix the error message but not root cause | Trace back to actual cause; don't just silence errors |
| **Assuming You Know** | Guess what's wrong; make change; hope it works | Form hypothesis; verify with tests; don't guess |
| **Missing Edge Cases** | Fix bug for common case but miss edge case | Think about boundary conditions; test them |
| **Not Searching for Related Issues** | Fix bug in one place; miss it in another | Search codebase for similar patterns |
| **Skipping Regression Testing** | Fix bug then break something else | Run full test suite after any fix |

---

## Related Patterns and Playbooks

- **Pattern:** [TDD](../patterns/test-driven-development.md) — Write tests that catch bugs
- **Pattern:** [Feedback Loops](../patterns/feedback-loops.md) — Use bugs as feedback to improve
- **Playbook:** [Review Implementation](review-implementation.md) — Catch bugs during code review
- **Process:** [Software Development Process - Phase 5/6](../processes/software-development.md) — Where bug investigation fits

---

## References

- Debugging Best Practices
- Root Cause Analysis Techniques
- Testing Strategy and Edge Case Coverage
- [TDD Pattern](../patterns/test-driven-development.md) for test-driven debugging
