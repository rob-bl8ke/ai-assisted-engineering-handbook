# Test-Driven Development (TDD)

A development approach where tests are written before implementation, guiding design and ensuring correctness throughout the development cycle.

---

## Pattern Definition

Test-Driven Development follows a disciplined cycle:

```
1. RED: Write a test that fails (because the feature doesn't exist yet)
   ↓
2. GREEN: Write minimum code to make the test pass
   ↓
3. REFACTOR: Improve code while keeping tests passing
   ↓
Repeat for next requirement
```

This cycle is also called **Red-Green-Refactor**.

---

## Why TDD Matters in AI-Assisted Development

TDD is particularly valuable when working with AI:

1. **Clear Requirements:** Tests define what "done" means unambiguously
2. **Verification:** Tests prove the AI's implementation is correct
3. **Regression Prevention:** Tests catch when changes break existing behavior
4. **Design Guidance:** Test structure guides the AI toward good design
5. **Confidence:** Complete test coverage means you can refactor without fear

---

## When to Use TDD

**Use TDD when:**
- Building new functionality (tests guide design)
- Fixing bugs (write test that reproduces bug, then fix)
- Refactoring (tests ensure behavior doesn't change)
- Working with AI (tests verify correctness)
- Requirements might change (tests document contract)

**TDD is particularly effective with AI because:**
- AI can see exactly what's expected (the test)
- AI implementation can be verified (tests pass)
- AI can't drift into scope creep (tests define scope)

---

## The Red-Green-Refactor Cycle

### Phase 1: RED — Write the Test First

Write a test for a single requirement:

```
// Example: Testing user registration
describe("User Registration", () => {
  test("should create a new user with valid email and password", () => {
    // Arrange: Set up test data
    const userData = { email: "alice@example.com", password: "SecurePass123" };
    
    // Act: Call the function to test
    const user = registerUser(userData);
    
    // Assert: Verify the result
    expect(user.email).toBe("alice@example.com");
    expect(user.id).toBeDefined();
  });
});
```

**At this point:**
- ❌ Test fails (function doesn't exist)
- ✓ Requirements are clear
- ✓ Success criteria are explicit

### Phase 2: GREEN — Write Minimum Code to Pass

Write the simplest possible code that makes the test pass:

```
// Minimum implementation
function registerUser(userData) {
  return {
    email: userData.email,
    id: generateId()
  };
}
```

**At this point:**
- ✓ Test passes
- ✓ You've proven the approach works
- ✓ Code is not yet refined (and that's okay)

### Phase 3: REFACTOR — Improve While Keeping Tests Green

Now improve the code without changing behavior:

```
// Refactored implementation
class UserService {
  registerUser(userData) {
    this.validateEmail(userData.email);
    this.validatePassword(userData.password);
    
    const user = new User(userData);
    user.passwordHash = this.hashPassword(userData.password);
    this.userRepository.save(user);
    
    return user;
  }
  
  validateEmail(email) {
    if (!email.match(emailRegex)) {
      throw new ValidationError("Invalid email");
    }
  }
  
  // ... other methods
}
```

**At this point:**
- ✓ Tests still pass
- ✓ Code is better structured
- ✓ Additional concerns are handled (validation, hashing, persistence)
- ✓ You can refactor confidently because tests verify correctness

### Phase 4: Next Requirement

Write a test for the next requirement:

```
// Test: Reject weak passwords
test("should reject passwords shorter than 8 characters", () => {
  const userData = { email: "bob@example.com", password: "short" };
  expect(() => registerUser(userData)).toThrow(ValidationError);
});
```

Repeat the cycle: RED → GREEN → REFACTOR

---

## TDD with AI Agents

TDD is powerful when combined with AI:

### Setup Phase (Human)
1. Write tests that define requirements
2. Provide tests to the AI
3. Tell AI: "Make all these tests pass"

### Implementation Phase (AI)
1. AI reads tests
2. AI implements code to pass tests
3. AI runs tests to verify correctness

### Verification Phase (Human)
1. Review AI's implementation
2. Verify tests cover requirements
3. Decide if refactoring is needed

---

## Structure of a Good Test

A well-structured test has three parts:

### 1. Arrange (Setup)
Prepare test data and state:

```
const user = new User();
const database = mockDatabase();
```

### 2. Act (Execute)
Call the code being tested:

```
const result = userService.findByEmail("alice@example.com");
```

### 3. Assert (Verify)
Check the result:

```
expect(result.email).toBe("alice@example.com");
expect(database.queried).toEqual(true);
```

**One test = one behavior**

Each test should verify a single, specific behavior.

---

## Test Coverage: Different Types of Tests

Different tests serve different purposes:

| Type | Purpose | Example |
|------|---------|---------|
| **Unit Tests** | Test single functions/methods in isolation | `registerUser()` returns correct user object |
| **Integration Tests** | Test how components work together | `registerUser()` saves to database and sends email |
| **Contract Tests** | Test API contracts between systems | Endpoint returns expected JSON structure |
| **End-to-End Tests** | Test complete user workflows | User visits site, signs up, receives email, logs in |

A good test suite includes all types:
- **Many** unit tests (fast, specific)
- **Some** integration tests (slower, valuable)
- **Few** end-to-end tests (slowest, catch high-level issues)

The software development process emphasizes unit tests for AI implementation work (quick feedback), with integration/E2E tests added in validation phases.

---

## Common Pitfalls

### ❌ Writing Tests After Code

**Problem:** Tests become afterthoughts; they test what was built instead of what's needed.

**Impact:**
- Tests don't guide design
- Code structure is less testable
- Tests miss important edge cases
- Tests become documentation of mistakes

**Fix:** Write tests first, always. This forces you to think about requirements before implementing.

### ❌ Tests That Test Implementation, Not Behavior

**Problem:** Tests are tightly coupled to implementation details.

```
// Bad: Tests implementation
test("should call getUserFromDatabase", () => {
  expect(database.query).toHaveBeenCalled();
  // What does this tell us about behavior?
});

// Good: Tests behavior
test("should retrieve user by email", () => {
  const user = userService.findByEmail("alice@example.com");
  expect(user.email).toBe("alice@example.com");
  // This tests the contract, not the implementation
});
```

**Fix:** Test behavior and outcomes, not implementation details.

### ❌ Tests That Are Too Complex

**Problem:** Tests are as complex as the code they're testing.

**Impact:**
- Tests have bugs
- Tests are hard to maintain
- Tests don't clarify requirements

**Fix:** Keep tests simple and focused:
- One assertion per test (or tightly related assertions)
- Clear test names that describe what's being tested
- Minimal setup (use test fixtures if setup is complex)

### ❌ Missing Edge Cases

**Problem:** Tests only cover the happy path.

```
// What about:
test("should register user", () => { /* happy path */ });

// Missing tests:
test("should reject duplicate email");
test("should reject invalid email");
test("should reject weak password");
test("should handle database error");
```

**Fix:** Systematically think about edge cases:
- What can go wrong?
- What are boundary conditions?
- What should happen on error?
- Write tests for each case

### ❌ Mocking Everything

**Problem:** Over-mocking makes tests pass but code doesn't work in reality.

```
// Too much mocking
test("should save user", () => {
  const fakeDB = { save: jest.fn() };
  userService.saveUser(user, fakeDB);
  expect(fakeDB.save).toHaveBeenCalled();
  // Does this actually test anything?
});
```

**Fix:** Mock external dependencies, but test real behavior:
```
// Better: Real behavior with mocked persistence
test("should save user", () => {
  const userService = new UserService(mockDatabase);
  userService.registerUser(userData);
  const saved = mockDatabase.users[0];
  expect(saved.email).toBe(userData.email);
  // Tests the real behavior with a mock database
});
```

---

## TDD in Different Scenarios

### Building New Features

1. Write test for a single requirement
2. Implement code to pass test
3. Refactor
4. Repeat for next requirement

### Fixing Bugs

1. Write test that reproduces the bug (test fails)
2. Implement the fix (test passes)
3. The fix is verified by the test

### Refactoring

1. Ensure all tests pass (before refactoring)
2. Refactor the code
3. Verify tests still pass
4. If any test fails, the refactoring broke something

---

## Benefits

- **Confidence:** Complete test coverage means you can refactor without fear
- **Design Guidance:** Tests guide toward simpler, more testable designs
- **Clear Requirements:** Tests are executable specifications
- **Documentation:** Tests show how code is supposed to be used
- **Fewer Bugs:** Issues caught during development, not in production
- **Faster Development:** Up-front design reduces rework
- **AI Verification:** AI's implementation is verified by tests

---

## TDD Best Practices

1. **Test one thing** — Each test verifies one behavior
2. **Use clear names** — Test names should describe what's being tested
3. **Test behavior, not implementation** — Test what the code does, not how
4. **Keep tests simple** — Tests should be simpler than the code being tested
5. **Maintain tests** — Tests are code too; they need maintenance
6. **Run tests frequently** — Run tests after every change
7. **Test edge cases** — Happy path is not enough
8. **Use test fixtures** — Reduce test setup duplication

---

## Related Patterns

- **[Vertical Slicing](vertical-slicing.md)** — Each slice is tested completely
- **[Tracer Bullets](tracer-bullets.md)** — Tracer bullets have working tests that verify the approach
- **[TDD Goal Loop](tdd-goal-loop/README.md)** — Coordinates specialized test-writer, code-writer, verifier, and evaluator agents through TDD slices
- **[Feedback Loops](feedback-loops.md)** — Tests provide feedback that shapes next iteration

---

## References

- "Test-Driven Development: By Example" by Kent Beck
- "Growing Object-Oriented Software, Guided by Tests" by Steve Freeman and Nat Pryce
- "Working Effectively with Legacy Code" by Michael Feathers

---

**Last Updated:** 2026-08-16  
**Status:** Core Pattern Documentation (MVP)
