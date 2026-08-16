# Ralph Loop: OpenCode Adapter

**Harness:** OpenCode (VS Code, JetBrains, vim)  
**Prerequisite:** [Ralph Loop Pattern (Core Concept)](../README.md)

---

## Overview

This adapter documents OpenCode-specific behavior and integration for Ralph Loops. OpenCode is a code-focused AI assistant designed for autonomous code generation and optimization:

1. **Code-First Approach:** Optimized for code generation, testing, and refactoring
2. **Repository Integration:** Understands repository structure deeply
3. **Local Execution:** Can run tests and analyze results locally
4. **Context Management:** Maintains file-level and repository-level context
5. **Autonomous Capability:** Can work without human intervention for straightforward tasks

---

## OpenCode Setup

### Prerequisites

1. **OpenCode Installation:** Via package manager or direct download
2. **Repository Access:** OpenCode should have read/write access to repository
3. **Test Framework:** Working test suite (`npm test`, `pytest`, etc.)
4. **Git Integration:** Repository initialized with git
5. **Environment Variables:** Any necessary secrets (API keys, database URLs)

### Configuration

**File: .opencode/config.json**
```json
{
  "model": "code-llama-34b",
  "maxTokens": 4096,
  "temperature": 0.2,
  "testCommand": "npm test",
  "commitMessageFormat": "conventional"
}
```

---

## OpenCode Ralph Loop Workflow

### Initialization

```bash
# Initialize OpenCode in repository
opencode init

# Configure for Ralph Loop
opencode config set ralph_loop true
opencode config set auto_commit true
opencode config set pause_on_blocker true
```

### Phase 1: Write Failing Test

```bash
# Tell OpenCode to write a test
opencode ralph-loop \
  --issue "issue-001.md" \
  --step 1 \
  --directive "Write a failing test for: Users can sign up with email and password"
```

**OpenCode Actions:**
1. Reads issue specification
2. Examines current test structure
3. Generates failing test for acceptance criterion
4. Saves to `test/auth/signup.test.ts`
5. Runs `npm test` to verify it fails
6. Reports results and awaits confirmation

### Phase 2: Implement Code

```bash
# Tell OpenCode to implement code
opencode ralph-loop \
  --issue "issue-001.md" \
  --step 2 \
  --directive "Implement minimum code to pass the failing test"
```

**OpenCode Actions:**
1. Reads failing test
2. Analyzes test requirements
3. Generates minimal implementation
4. Saves to `src/auth/signup.ts`
5. Runs `npm test` to verify tests pass
6. Reports implementation and awaits confirmation

### Phase 3+: Continue Loop

```bash
# Repeat for each acceptance criterion
opencode ralph-loop --issue "issue-001.md" --step 3
opencode ralph-loop --issue "issue-001.md" --step 4
# ... continue until complete
```

---

## OpenCode CLI for Ralph Loop

### Command: ralph-loop

**Syntax:**
```bash
opencode ralph-loop [options]
```

**Options:**

| Option | Type | Description |
|--------|------|-------------|
| `--issue` | string | Path to issue specification file |
| `--step` | number | Current step in loop (1=test, 2=code, 3=test, etc.) |
| `--directive` | string | Specific task for this iteration |
| `--auto-apply` | boolean | Automatically apply changes (default: false, requires confirmation) |
| `--pause-on-blocker` | boolean | Pause if blocker encountered (default: true) |
| `--max-steps` | number | Maximum iterations before giving up (default: 20) |
| `--verbose` | boolean | Print detailed output |

**Examples:**

```bash
# Basic Ralph Loop from issue specification
opencode ralph-loop --issue issue-001.md

# Step-by-step with confirmation
opencode ralph-loop --issue issue-001.md --step 1 --directive "Write test for criterion 1"

# Autonomous without confirmation
opencode ralph-loop --issue issue-001.md --auto-apply

# With verbose output
opencode ralph-loop --issue issue-001.md --verbose

# Resume from specific step
opencode ralph-loop --issue issue-001.md --step 7
```

---

## Acceptance Criteria Checking

OpenCode can understand and track acceptance criteria:

```bash
# List acceptance criteria from issue
opencode criteria list issue-001.md

# Check which criteria are met
opencode criteria status

# Verify implementation covers all criteria
opencode criteria verify
```

**Output:**
```
Acceptance Criteria Status:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✓ Users can sign up with email and password
✓ Passwords are hashed using bcrypt
✗ Sign up fails if email already exists
✗ Session tokens are generated after successful login

Progress: 2/4 criteria met (50%)
Next: Implement duplicate email detection
```

---

## OpenCode Context Management

### Repository Context

OpenCode automatically indexes:

```
Repository State:
├─ Code structure (files, imports, dependencies)
├─ Test patterns (test files, test utilities)
├─ Build configuration (package.json, tsconfig.json, etc.)
├─ Git history (recent commits, branch info)
├─ Open files and selections
└─ Environment variables
```

### File Watching

OpenCode can watch for changes:

```bash
# Watch for file changes and update context
opencode watch src/auth test/auth
```

When files change, OpenCode updates context for next iteration.

### Cache Management

```bash
# Clear OpenCode cache (if context becomes stale)
opencode cache clear

# Show cache size
opencode cache info
```

---

## Blocker Detection and Escalation

### Automatic Blocker Detection

OpenCode recognizes common blockers:

```
1. Ambiguous acceptance criteria (needs clarification)
2. Missing prerequisites (dependencies, files, infrastructure)
3. Architecture conflicts (contradicts established patterns)
4. Test failures (implementation incomplete or incorrect)
5. Infrastructure issues (database, API, network errors)
```

### Manual Blocker Escalation

If you encounter an issue, explicitly escalate:

```bash
# Report a blocker
opencode blocker report \
  --issue "issue-001.md" \
  --message "Password hashing strategy unclear: bcrypt vs. argon2?"
```

OpenCode pauses and waits for resolution.

### Blocker Resolution

```bash
# Resume after resolving blocker
opencode blocker resolve \
  --message "Use bcrypt-10 (consistent with team preference)"

# Resume loop
opencode ralph-loop --issue issue-001.md --step 5
```

---

## OpenCode vs. Claude for Ralph Loop

| Aspect | OpenCode | Claude |
|--------|----------|--------|
| **Autonomy** | Fully autonomous (runs without confirmation) | Pauses for confirmation at each step |
| **Local Execution** | Runs tests locally; sees results immediately | Human must run tests and report |
| **Blocker Handling** | Auto-detects common blockers; pauses on edge cases | Requires prompt to identify blockers |
| **Context** | Maintains persistent context across iterations | Requires context in each API call |
| **Cost** | One-time setup; no per-iteration costs | Pay-per-API-call (~$0.01 per iteration) |
| **Speed** | Instant feedback (local execution) | ~2 second latency per call |
| **Best for** | Large projects, high volume iterations | One-off tasks, varying requirements |

---

## Best Practices for OpenCode Ralph Loop

### Practice 1: Clear Issue Specifications

OpenCode works best with detailed issue specs:

```markdown
# Issue: Add User Authentication

## Overview
Users need to sign up and log in with email/password.

## Acceptance Criteria
- Users can sign up with email and password
- Passwords are hashed using bcrypt-10
- Sign up fails if email already exists (return 409 Conflict)
- Session tokens are generated after successful login (JWT, 24h expiry)

## Technical Details
- Language: TypeScript
- Test framework: Jest
- Database: PostgreSQL
- Password hashing: bcrypt
- Session storage: Redis

## Related Files
- src/auth/signup.ts (new)
- test/auth/signup.test.ts (new)
- src/database/users.ts (existing)

## Definition of Done
- All acceptance criteria passing tests
- Code follows TypeScript style guide
- Commit history is clear
- Ready for code review
```

### Practice 2: Monitor Progress

```bash
# Watch loop progress in real-time
opencode ralph-loop --issue issue-001.md --verbose

# In separate terminal, monitor files
watch -n 1 'ls -la src/auth/ test/auth/'
```

### Practice 3: Pause and Learn

Even in autonomous mode, you can pause to understand decisions:

```bash
# Pause after step 2 (after implementation)
opencode ralph-loop --issue issue-001.md --step 2

# Review generated code
cat src/auth/signup.ts

# Understand OpenCode's reasoning
opencode explain step:2
```

### Practice 4: Customize Behavior

```bash
# Use different model for complex tasks
opencode config set model "code-llama-70b"

# Reduce temperature for more conservative code
opencode config set temperature 0.1

# Re-run Ralph Loop
opencode ralph-loop --issue issue-001.md
```

### Practice 5: Integration with Scripts

Combine OpenCode with Bash/PowerShell:

```bash
#!/bin/bash
# run-ralph-loop.sh

ISSUE="issue-001.md"
START_STEP="${1:-1}"

# Run OpenCode Ralph Loop autonomously
opencode ralph-loop \
  --issue "$ISSUE" \
  --step "$START_STEP" \
  --verbose

# After loop completes, run additional checks
npm run lint
npm run typecheck

# If all good, create PR
if [ $? -eq 0 ]; then
  gh pr create --title "Issue: $(head -1 $ISSUE)" --body "$(cat $ISSUE)"
fi
```

---

## Known OpenCode Limitations

### Limitation 1: Repository Size
OpenCode may struggle with very large repositories.

**Mitigation:**
- Use `opencode focus` to limit context to specific folder
- Pre-index relevant files: `opencode index src/auth test/auth`
- Split large issues into smaller ones

### Limitation 2: External API Calls
OpenCode can't natively make external API calls (but can generate code that does).

**Workaround:**
- Mock external services in tests
- Use environment variables for API keys
- Test against local/dev endpoints

### Limitation 3: Complex Architecture Decisions
OpenCode works best on straightforward implementations.

**Mitigation:**
- Pre-decide architecture in issue description
- Provide context files that show established patterns
- Reserve complex decisions for manual implementation

---

## Troubleshooting OpenCode Ralph Loop

### Issue: "Context window exceeded"

**Cause:** Repository too large for single context.  
**Solution:**

```bash
# Focus on specific directories
opencode ralph-loop --issue issue-001.md --focus "src/auth test/auth"

# Or limit history
opencode config set max_git_history 10
```

### Issue: "Test execution failed"

**Cause:** Test framework not properly configured or dependencies missing.  
**Solution:**

```bash
# Verify test setup manually
npm test

# Clear OpenCode cache and retry
opencode cache clear
opencode ralph-loop --issue issue-001.md --step 1
```

### Issue: "Generated code doesn't match style"

**Cause:** OpenCode's style different from project conventions.  
**Solution:**

```bash
# Provide style guide
opencode style guide add .eslintrc.json
opencode style guide add .prettierrc

# Retry loop
opencode ralph-loop --issue issue-001.md
```

### Issue: "Loop terminates early"

**Cause:** Blocker detected; OpenCode paused.  
**Solution:**

```bash
# Check blocker
opencode blocker show

# Resolve and continue
opencode blocker resolve --message "[resolution]"
opencode ralph-loop --issue issue-001.md --resume
```

---

## Example: Full OpenCode Ralph Loop Session

```bash
$ opencode ralph-loop --issue issue-001.md --verbose

[INIT] Ralph Loop Starting
[INIT] Issue: Add User Authentication
[INIT] Model: code-llama-34b
[INIT] Max steps: 20

[STEP 1] Writing test for: Users can sign up with email and password
[STEP 1] Analyzing current test structure...
[STEP 1] Generating test code...
[STEP 1] Creating test/auth/signup.test.ts
[STEP 1] Running: npm test
[STEP 1] ✗ Test failed (as expected)

[STEP 2] Writing implementation code
[STEP 2] Analyzing test requirements...
[STEP 2] Generating implementation...
[STEP 2] Creating src/auth/signup.ts
[STEP 2] Running: npm test
[STEP 2] ✓ Test passed!
[STEP 2] Committing: feat(auth): implement basic signup

[STEP 3] Writing test for: Passwords are hashed using bcrypt
[STEP 3] Generating test code...
[STEP 3] Creating test/auth/signup.test.ts (appending)
[STEP 3] Running: npm test
[STEP 3] ✗ Test failed (as expected)

[STEP 4] Implementing password hashing
[STEP 4] Analyzing test requirements...
[STEP 4] Generating implementation...
[STEP 4] Updating src/auth/signup.ts
[STEP 4] Running: npm test
[STEP 4] ✓ All tests passed!
[STEP 4] Committing: feat(auth): add bcrypt password hashing

[STEP 5] Writing test for: Sign up fails if email exists
[STEP 5] Generating test code...
[STEP 5] Running: npm test
[STEP 5] ✗ Test failed (as expected)

[STEP 6] Implementing duplicate email check
[STEP 6] Analyzing test requirements...
[STEP 6] Generating implementation...
[STEP 6] Running: npm test
[STEP 6] ✓ All tests passed!
[STEP 6] Committing: feat(auth): add duplicate email validation

[STEP 7] Writing test for: Session tokens generated after login
[STEP 7] Generating test code...
[STEP 7] Running: npm test
[STEP 7] ✗ Test failed (as expected)

[STEP 8] Implementing session token generation
[STEP 8] Analyzing test requirements...
[STEP 8] Generating implementation...
[STEP 8] Running: npm test
[STEP 8] ✓ All tests passed!
[STEP 8] Committing: feat(auth): add JWT session token generation

[VERIFY] All acceptance criteria met!
[VERIFY] Running full test suite...
[VERIFY] ✓ 12 tests passed
[VERIFY] ✓ Code style check passed
[VERIFY] ✓ TypeScript compilation passed

[COMPLETE] Ralph Loop finished successfully
[COMPLETE] Ready for code review
[COMPLETE] Commit history: 4 commits
[COMPLETE] Files changed: 3 (test, src, package.json)
```

---

## See Also

- **[Ralph Loop Pattern (Core Concept)](../README.md)** — Harness-agnostic pattern definition
- **[Claude Adapter](./claude.md)** — Claude integration for autonomous Ralph Loops
- **[Copilot Adapter](./copilot.md)** — GitHub Copilot integration
- **[Bash Implementation](../examples/bash.md)** — Scripted Ralph Loop
- **[PowerShell Implementation](../examples/powershell.md)** — Windows scripted Ralph Loop
