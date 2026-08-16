# Ralph Loop Pattern

**Status:** Core Pattern  
**Operationalizes:** [AFK Implementation (Phase 4.2)](../../processes/software-development.md#42-afk-implementation-ralph-loop)  
**Related Patterns:** [Tracer Bullets](../tracer-bullets.md), [Test-Driven Development](../test-driven-development.md), [Feedback Loops](../feedback-loops.md), [Vertical Slicing](../vertical-slicing.md)

---

## Core Concept

A Ralph Loop is an **autonomous agent feedback loop** where an AI agent:

1. **Receives** a complete, well-specified task (GitHub issue with acceptance criteria)
2. **Implements** the task end-to-end using established practices (TDD, tracer bullets, patterns)
3. **Commits** intermediate progress with clear commit messages explaining decisions
4. **Documents** the work: code comments, test descriptions, commit rationale
5. **Encounters a blocker** → Pauses and documents the obstruction explicitly
6. **Returns control** to human for clarification or resolution
7. **Resumes** from the blocker with new information
8. **Completes** when acceptance criteria are met and tests pass

**Key characteristic:** The agent works autonomously *until a decision point requires human judgment* or clarification, then pauses (does not hallucinate or assume). The loop completes when the issue is resolved or escalation is required.

### Why "Ralph"?

Named after [Matt Pocock's demonstration](../references/matt-pocock.md) of using Claude with a Bash script to implement code autonomously in a loop. The pattern generalizes this technique across platforms and AI harnesses.

---

## Core Decision Points

### 1. Is the task specification complete?
- **YES:** Proceed to step 2 (Implementation)
- **NO:** Document ambiguities; request clarification from human; pause loop
- **Verification:** Check for: clear acceptance criteria, linked context, defined done

### 2. Are prerequisite dependencies available?
- **YES:** Proceed to step 3 (Environment setup)
- **NO:** Document missing dependencies; request resolution; pause loop
- **Verification:** Can agent access repository? Can agent run tests? Are all code dependencies available?

### 3. Is test infrastructure in place?
- **YES:** Proceed to step 4 (TDD cycle)
- **NO:** Set up test framework as first step; document test strategy; proceed to step 4
- **Verification:** Can tests be run? Do tests execute in < 30 seconds on typical hardware?

### 4. Does the current test suite pass?
- **YES:** Proceed to step 5 (Red-Green-Refactor)
- **NO:** Document failing tests; request clarification on acceptance criteria; pause loop
- **Verification:** Run full test suite; confirm all tests pass

### 5. Are all acceptance criteria met?
- **YES:** Proceed to step 6 (Code review & completion)
- **NO:** Identify next failing criterion; write test; implement feature; loop to step 4
- **Verification:** Run acceptance tests explicitly; document which criteria are covered

### 6. Is the code ready for human review?
- **YES:** Commit final changes; pause loop (complete)
- **NO:** Refactor, add documentation, clean up; loop to step 5
- **Verification:** Commit message is clear? Code follows style guide? Tests cover edge cases?

---

## Decision Flow

```
Start (Issue received)
    │
    ├─→ [1. Task specification complete?]
    │   ├─ NO → Document ambiguities → Pause (request clarification)
    │   └─ YES ↓
    │
    ├─→ [2. Prerequisites available?]
    │   ├─ NO → Document blockers → Pause (request resolution)
    │   └─ YES ↓
    │
    ├─→ [3. Test infrastructure in place?]
    │   ├─ NO → Set up tests → Continue
    │   └─ YES ↓
    │
    ├─→ [4. Current test suite passes?]
    │   ├─ NO → Document failures → Pause (request clarification)
    │   └─ YES ↓
    │
    ├─→ [5. All acceptance criteria met?]
    │   ├─ NO → Write test for next criterion → Implement → Loop to [4]
    │   └─ YES ↓
    │
    ├─→ [6. Code ready for review?]
    │   ├─ NO → Refactor, document, clean up → Loop to [5]
    │   └─ YES ↓
    │
    └─→ Commit final changes → Complete
        (Loop pauses; ready for human review)
```

---

## Canonical Behavior: Steps in the Loop

### Setup Phase (Pre-Loop)

1. **Receive Task:**
   - Read GitHub issue specification
   - Review linked context and acceptance criteria
   - Check for blockers or ambiguities
   - Request clarification if needed (Decision Point 1)

2. **Verify Prerequisites:**
   - Confirm repository access and structure
   - Confirm test framework availability
   - Confirm all code dependencies are resolvable (Decision Point 2)
   - Set up development environment if needed

### Loop Phase: Test-Driven Implementation

3. **Establish Test Suite:**
   - Review existing tests related to feature
   - Identify acceptance criteria that need tests
   - Write one failing test that embodies an acceptance criterion (Decision Point 3)
   - Run test suite; confirm it fails

4. **Implement Minimum Code:**
   - Write *minimal* code to make the failing test pass
   - Do not over-engineer or anticipate future features
   - Run test suite; confirm it passes (Decision Point 4)
   - Commit with message: `tests: add acceptance test for [criterion]` or `feat: [feature] to satisfy [criterion]`

5. **Evaluate Progress:**
   - Check acceptance criteria checklist
   - If more criteria remain unmet:
     - Write test for next criterion
     - Loop to step 4
   - If all criteria met:
     - Proceed to step 6 (Decision Point 5)

6. **Refine & Document:**
   - Add code comments explaining non-obvious decisions
   - Add edge case tests if not yet covered
   - Ensure all tests are readable and document the feature
   - Update README or documentation if API changed
   - Commit: `docs: [documentation]` or `refactor: [cleanup]`
   - Confirm test suite still passes

7. **Final Verification:**
   - Run full test suite one final time
   - Confirm acceptance criteria are explicitly testable
   - Confirm commit history tells a clear story (Decision Point 6)
   - Prepare summary of work for code review

### Escalation / Pause Points

**Pause and Request Clarification When:**
- Acceptance criteria are ambiguous or contradictory
- Prerequisite code/dependencies are missing
- Architecture decisions require product/tech lead input
- Proposed solution conflicts with established patterns
- Test infrastructure is insufficient
- Edge cases or security concerns are unclear

**Document the Pause Explicitly:**
- Create a comment with `[BLOCKED]` header
- Explain what decision is required
- Provide context: what was attempted, what failed
- Suggest resolution approaches if possible
- Request specific guidance from human

---

## How to Recognize Blocker Scenarios

### Scenario: Ambiguous Acceptance Criteria

**Example:**
```
Acceptance Criteria:
- Feature is "performant"
- User experience is "smooth"
```

**Detection:** No measurable threshold (< 100ms? < 1 second?), multiple interpretations possible

**Action:**
```
[BLOCKED] Ambiguous acceptance criteria

Criterion: "Feature is performant"
Question: What quantitative threshold defines performance? 
  - < 100ms? 
  - < 1s? 
  - Relative to current implementation?

Current approach: Implemented caching layer. Reduced load time from 2s to 300ms.
Needs clarification: Is 300ms acceptable?
```

### Scenario: Missing Architecture Decision

**Example:**
```
Acceptance Criteria:
- Feature integrates with payment system
- No credentials in code
```

**Detection:** Multiple valid approaches (env vars, secrets manager, hardcoded secrets service), needs architectural choice

**Action:**
```
[BLOCKED] Architecture decision required

Question: How should payment API credentials be handled?
  
Options explored:
1. Environment variables (simplest, used by other services)
2. Secrets manager (most secure, new infrastructure)
3. Hardcoded service endpoint (not secure)

I recommend Option 1 (consistency), but this affects:
- Deployment configuration
- CI/CD environment variables
- Local development setup

Please confirm approach.
```

### Scenario: Prerequisite Missing

**Example:**
```
Acceptance Criteria:
- Feature uses the UserContext API
```

**Detection:** UserContext API not yet implemented or not documented

**Action:**
```
[BLOCKED] Prerequisite not available

Issue asks for integration with UserContext API, but API is not yet implemented.
- Searched code: No UserContext implementation found
- Checked issue tracker: UserContext is in #8 (not yet complete)
- Checked documentation: No API specification

Blocker: Cannot implement this issue before UserContext is complete.

Recommendation: Defer this issue until #8 completes, or adjust acceptance criteria to not require UserContext.
```

---

## Verification Checklist

Use this checklist to verify that a Ralph Loop implementation is complete and ready for human review:

- [ ] **Task Completeness:** Issue specification includes acceptance criteria (all explicitly testable)
- [ ] **Test Coverage:** Every acceptance criterion has at least one test
- [ ] **Tests Pass:** Full test suite passes; no flaky tests
- [ ] **TDD Applied:** Code follows test-driven workflow (red → green → refactor)
- [ ] **Tracer Bullet:** First working implementation is end-to-end (not isolated components)
- [ ] **Pattern Compliance:** Code follows established patterns (naming, structure, error handling)
- [ ] **Documentation:** Code is commented; non-obvious decisions are explained
- [ ] **Commit History:** Each commit message explains the feature or fix (not "WIP" or "fixes")
- [ ] **Blockers Documented:** If paused, blocker is documented with context and questions
- [ ] **No Assumptions:** Agent did not assume requirements; asked for clarification when ambiguous
- [ ] **Ready for Review:** Code is clean, tested, and ready for human code review

---

## Platform Equivalence

Ralph Loops are **harness-agnostic** but implementations vary by platform:

| Aspect | Bash | PowerShell | Language |
|--------|------|-----------|----------|
| **Shell Loop** | `while true; do ... done` | `while ($true) { ... }` | Native shell |
| **Piping** | `\|` (stdout) | `\|` (object pipeline) | Language-native |
| **Variable Expansion** | `${VAR}` | `$VAR` | Shell syntax |
| **Error Handling** | `set -e` + `if [ $? -ne 0 ]` | `if ($LastExitCode -ne 0)` | Language-native |
| **File Operations** | `cat`, `grep`, `sed` | `Get-Content`, `Select-String`, etc. | Language-native |
| **Agent Invocation** | `curl`, `ollama`, `aws` CLI | `Invoke-WebRequest`, SDK | Language-native |

**Principle:** The workflow is identical; only the shell syntax and tool invocation differ.

---

## Canonical Ralph Loop Algorithm (Pseudocode)

This is how any harness should implement the Ralph Loop:

```
FUNCTION ralph_loop(issue_specification):
  
  /// SETUP PHASE
  task ← parse_issue(issue_specification)
  IF NOT task.is_complete():
    request_clarification(task)
    RETURN

  IF NOT verify_prerequisites(task):
    document_blockers(task)
    RETURN

  /// LOOP PHASE: TDD Implementation
  WHILE NOT all_acceptance_criteria_met(task):
    
    // Step 1: Write Failing Test
    criterion ← next_unmet_criterion(task)
    test ← write_test_for(criterion)
    run_tests()
    
    IF test_failed():
      // Step 2: Implement Minimum Code
      code ← write_minimal_code_to_pass(test)
      run_tests()
      
      IF test_passed():
        commit_with_message("feat: " + criterion_description)
        mark_criterion_tested(criterion)
      ELSE:
        document_blocker("Test failed; architecture unclear")
        request_clarification()
        RETURN
    ELSE:
      document_blocker("Test already passing; criterion already met")
  
  /// STEP 3: Refine & Finalize
  refactor_for_readability()
  add_edge_case_tests()
  update_documentation()
  commit_with_message("refactor: cleanup and finalize")
  
  run_tests()  // Final verification
  
  RETURN code_ready_for_review()
```

---

## Integration Points

### Process Integration

- **Phase 4.2 (AFK Implementation):** Ralph Loop defines the canonical implementation workflow
- **Phase 5 (Review):** Human reviews completed implementation and passes/fails work
- **Phase 6 (Integration):** Completed code is merged

### Pattern Integration

- **[Tracer Bullets](../tracer-bullets.md):** First implementation should be end-to-end and minimal
- **[Test-Driven Development](../test-driven-development.md):** Ralph Loop is TDD in motion
- **[Feedback Loops](../feedback-loops.md):** Ralph Loop is a feedback loop at the implementation level
- **[Vertical Slicing](../vertical-slicing.md):** Ralph Loop works on individual vertical slices (one issue at a time)

### Skill Integration

- **[PRD to Issues Skill](../../skills/prd-to-issues.md):** Creates the specifications that Ralph Loops consume
- **[Code Reviewer Skill](../../skills/code-reviewer.md):** Reviews the output of Ralph Loops

---

## Common Pitfalls

### Pitfall 1: Insufficient Specification
**Problem:** Ralph Loop receives incomplete issue; makes assumptions and proceeds without clarification

**Prevention:**
- Decision Point 1: Explicitly verify specification completeness before starting
- Document blockers: If ambiguity found → pause and request clarification
- Reviewer checklist: "Does acceptance criteria use quantitative thresholds?"

### Pitfall 2: Over-Engineering
**Problem:** Ralph Loop anticipates future features and over-implements; violates "minimal code" principle

**Prevention:**
- Tracer Bullet principle: Implement the *simplest* code that passes the test
- Refactor only when test is green: Refactoring is a separate phase
- Commit message discipline: "feat: X to satisfy criterion Y" not "feat: X plus potential future Z"

### Pitfall 3: Skipping Failing Test
**Problem:** Ralph Loop writes test and immediately sees it pass (feature already exists); doesn't recognize duplicate work

**Prevention:**
- Run test *first* and confirm it fails (red phase is mandatory)
- Document why test should fail: "This test checks that [property] holds for [new feature]"
- If test already passes: Stop; mark criterion as already met; do not implement

### Pitfall 4: Blocker Hallucination
**Problem:** Ralph Loop encounters ambiguity and *invents* an answer instead of asking for clarification

**Prevention:**
- Define blocker scenarios clearly (see Blocker Scenarios section above)
- Require explicit escalation when encountering architecture decisions
- Reviewer checklist: "Are there [BLOCKED] comments that should have been escalated?"

### Pitfall 5: Test Suite Fragility
**Problem:** Tests are flaky or interdependent; Ralph Loop spends time debugging test infrastructure instead of feature

**Prevention:**
- Pre-loop verification: Confirm test suite is robust and tests run < 30 seconds
- Write isolated tests: Each test should be independent
- Document test assumptions: "This test assumes [prerequisite state]"

### Pitfall 6: Unclear Commit History
**Problem:** Ralph Loop makes many commits with vague messages ("WIP", "fixes", "cleanup"); code review is difficult

**Prevention:**
- Commit message discipline: Each commit should explain *what* and *why*
- Conventional Commits format: `type(scope): description`
- Verification step: Reviewer reads commit history; should understand implementation strategy

---

## Success Metrics

A successful Ralph Loop produces:

1. **Complete Implementation:** All acceptance criteria satisfied and tested
2. **Test Coverage:** Every criterion has at least one test; tests are readable
3. **Clear History:** Commit messages tell a coherent story of the implementation
4. **Minimal Code:** Implementation is focused on criteria; no speculative features
5. **Documented Decisions:** Non-obvious choices are explained in code comments or commit messages
6. **Zero Blocker Hallucination:** If paused, blocker is legitimate and well-documented
7. **Ready for Review:** Code follows patterns; no style violations; no TODOs left behind

---

## Related Reading

- **[Matt Pocock's Ralph Loop Implementation](../references/matt-pocock.md)** — Original demonstration with Bash and Claude
- **[AFK Implementation (Process Phase 4.2)](../../processes/software-development.md#42-afk-implementation-ralph-loop)** — How Ralph Loops fit in the development workflow
- **[Tracer Bullets Pattern](../tracer-bullets.md)** — Building minimal end-to-end implementations
- **[Test-Driven Development Pattern](../test-driven-development.md)** — TDD applied at larger scale
- **[Vertical Slicing Pattern](../vertical-slicing.md)** — How features are split for Ralph Loop processing
