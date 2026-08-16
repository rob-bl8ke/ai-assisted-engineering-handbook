# Ralph Loop: Claude Adapter

**Harness:** Claude (via API)  
**Prerequisite:** [Ralph Loop Pattern (Core Concept)](../README.md)

---

## Overview

This adapter documents Claude-specific behavior and optimizations for the Ralph Loop. Claude's capabilities are well-suited for Ralph Loops because:

1. **Extended Context:** Claude's 200K token context window can hold full repository state, issue specification, and implementation history
2. **Tool Use:** Claude can theoretically use tools to read/write files and run tests autonomously (though current implementation uses human-in-the-loop)
3. **Reasoning:** Claude's reasoning abilities help with complex architecture decisions and blocker diagnosis
4. **Artifact Generation:** Claude can generate complete, well-documented code blocks
5. **Thoughtful Escalation:** Claude clearly identifies when human judgment is needed

---

## Claude API Configuration

### Endpoint
```
https://api.anthropic.com/v1/messages
```

### Required Headers
```
Authorization: Bearer $CLAUDE_API_KEY
anthropic-version: 2023-06-01
content-type: application/json
```

### Model Selection

| Model | Best For | Notes |
|-------|----------|-------|
| `claude-3-5-sonnet-20241022` | **Recommended** | Fast, good reasoning, excellent code generation. ~$3/M tokens |
| `claude-3-opus-20240229` | Complex tasks | Slower, more powerful reasoning. ~$15/M tokens |
| `claude-3-haiku-20240307` | Simple tasks | Fastest, cheapest. ~$0.80/M tokens |

**Recommendation for Ralph Loop:** Use Sonnet by default; use Opus for architecture-heavy issues.

### Rate Limits
- **Standard:** 50,000 requests/min, 40M tokens/min
- **Higher tiers:** Higher limits available
- **Retry strategy:** Exponential backoff (1s, 2s, 4s, 8s)

---

## Claude-Specific Prompt Techniques

### 1. Role Definition
Claude responds better when given an explicit role:

```
You are an expert code implementer following the Ralph Loop pattern.
You are methodical, test-driven, and careful about edge cases.
```

### 2. Step-by-Step Instructions
Break down the task into numbered steps; Claude follows structure well:

```
INSTRUCTIONS:
1. Examine the acceptance criteria
2. [...]
3. [...]
```

### 3. Output Format Specification
Be explicit about how you want Claude to structure responses:

```
OUTPUT:
- Describe what you will implement
- Show the code changes (if any)
- Suggest a commit message
- If blocked, explain the blocker
```

### 4. Context Ordering
Put most important context first; Claude uses earlier context more effectively:

```
1. Task (most important)
2. Current repository state
3. Directive (what to do now)
4. Instructions (how to do it)
```

### 5. Blocker Signals
Claude understands structured markers; use consistent signals:

```
If you encounter ambiguity, document [BLOCKED] with explanation
```

Claude will reliably use this marker when appropriate.

---

## Common Prompt Patterns for Ralph Loop

### Pattern 1: Test Writing Prompt

```
You are implementing a feature following Test-Driven Development (TDD).

TASK:
[Issue specification]

ACCEPTANCE CRITERIA:
[List of criteria]

CURRENT STATE:
[Repository state, especially test results]

DIRECTIVE:
Write a failing test for the next acceptance criterion.
Do not implement code yet.

OUTPUT:
- Which criterion you're testing
- The test code (complete and runnable)
- Expected failure message
- Suggested test file name and location
```

### Pattern 2: Implementation Prompt

```
You are implementing a feature following Test-Driven Development (TDD).

TASK:
[Issue specification]

FAILING TEST:
[Test code that needs to pass]

CURRENT STATE:
[Repository state, especially code structure]

DIRECTIVE:
Implement the minimum code to make the failing test pass.
Do not add features beyond what the test requires.
Do not refactor yet.

OUTPUT:
- What you will implement
- The code changes (complete and ready to paste)
- Suggested commit message
- Any edge cases you noticed (but didn't implement)
```

### Pattern 3: Blocker Diagnosis Prompt

```
TASK:
[Issue specification]

BLOCKER ENCOUNTERED:
[What failed or was unclear]

CURRENT STATE:
[Repository state, error messages, test output]

REQUEST:
Diagnose the blocker. Is it:
1. Ambiguous requirements?
2. Missing prerequisites?
3. Architecture conflict?
4. Infrastructure issue?
5. Other?

OUTPUT:
- Root cause assessment
- Recommended resolution
- Clarifying questions (if needed)
- Blockers to escalate to human
```

---

## Token Usage & Cost Optimization

### Reduce Token Usage

**Problem:** Full repository state can be large.  
**Solutions:**

1. **Selective File Inclusion:**
   ```
   - Relevant code files only (not node_modules, build outputs)
   - File size limits (max 50KB per file)
   - Line count limits (show first/last 100 lines of large files)
   ```

2. **Condense Test Output:**
   ```
   # Instead of full test output:
   - 145 tests passed
   - 3 tests failed:
     • test/auth/signup.test.ts:23 (error: password hashing not implemented)
     • test/auth/login.test.ts:45 (error: session token generation not implemented)
     • test/auth/logout.test.ts:12 (error: logout not implemented)
   ```

3. **Abbreviated Repository State:**
   ```
   === Repository Structure ===
   src/
   ├─ auth/
   │  ├─ signup.ts (567 lines)
   │  ├─ login.ts (234 lines)
   │  └─ types.ts (89 lines)
   ├─ utils/
   └─ index.ts
   
   test/
   ├─ auth/
   │  ├─ signup.test.ts (340 lines)
   │  ├─ login.test.ts (287 lines)
   │  └─ logout.test.ts (156 lines)
   ```

### Cost Calculation

**Typical Ralph Loop Iteration:**
- Input tokens: ~3,000-5,000 (repository state + prompt)
- Output tokens: ~1,000-2,000 (code + explanation)
- **Total per iteration:** ~4,000-7,000 tokens
- **Cost per iteration (Sonnet):** ~$0.01-0.02
- **Cost per full feature (5 iterations):** ~$0.05-0.10

**To reduce costs:**
- Use `claude-3-5-haiku-20241022` for simple features
- Condense repository state (see above)
- Resume from step N instead of restarting

---

## Claude-Specific Capabilities

### Capability 1: Complex Reasoning
Claude excels at explaining why decisions are needed:

```
[BLOCKED] Architecture decision required

The test requires authentication, but the design doesn't specify:
- Should passwords be hashed with bcrypt or argon2?
- Should sessions use JWT or server-side storage?
- Should email verification be required?

Each choice affects test implementation and security posture.
I recommend: bcrypt + server-side sessions (familiar to team) + email verification (security requirement).

Please confirm.
```

### Capability 2: Edge Case Identification
Claude often identifies edge cases unprompted:

```
// Implementation note: Handling edge cases:
// 1. Email normalization (case-insensitive, trim whitespace)
// 2. SQL injection prevention (using parameterized queries)
// 3. Race condition on signup (concurrent requests for same email)
// 4. Password requirements (minimum length, complexity)

// Current implementation covers #1, #2, #3. #4 needs clarification.
```

### Capability 3: Multi-Language Support
Claude can generate code in any language in the same loop:

```
Current test file: test/auth/signup.test.ts (TypeScript/Jest)
Suggested implementation: src/auth/signup.ts (TypeScript)
Alternative: src/auth/signup.py (Python) if you prefer
```

---

## Known Claude Limitations

### Limitation 1: File I/O
Claude cannot directly read or write files. Current workaround: human applies changes.

**Mitigation:**
- Use explicit markers: `// START: signup.ts`, `// END: signup.ts`
- Claude can generate exact file content ready to paste
- Tool use (future): Claude will be able to write files directly

### Limitation 2: Real-Time Test Execution
Claude cannot run tests and see results in the same prompt. Current workaround: human runs tests and feeds results back.

**Mitigation:**
- Provide test output in next iteration
- Claude learns from failures and adjusts approach
- Alternative: Use Claude with tool integration (coming soon)

### Limitation 3: Long Context Overhead
With 200K tokens available, it's tempting to include everything. This increases latency and cost.

**Mitigation:**
- Keep repository state condensed (~2K tokens)
- Focus on changed files and test output
- Remove unchanged, irrelevant files

### Limitation 4: Blocker Escalation
Claude sometimes over-escalates or under-escalates blockers.

**Mitigation:**
- Provide examples in prompt: "Here's what counts as a blocker vs. a design choice"
- Give Claude authority to make certain decisions: "You can choose between bcrypt and argon2 as long as tests pass"
- Escalate ambiguous cases: "[BLOCKED] Please clarify: Should X or Y?"

---

## Integration: Claude + Ralph Loop Script

### Bash Integration (from bash.md)
```bash
call_claude() {
  local prompt="$1"
  
  curl -s -X POST "$CLAUDE_API_URL" \
    -H "x-api-key: $CLAUDE_API_KEY" \
    -H "anthropic-version: 2023-06-01" \
    -H "content-type: application/json" \
    -d "{
      \"model\": \"claude-3-5-sonnet-20241022\",
      \"max_tokens\": 4096,
      \"messages\": [{\"role\": \"user\", \"content\": \"$prompt\"}]
    }"
}
```

### PowerShell Integration (from powershell.md)
```powershell
function Invoke-Claude {
    param([string]$Prompt)
    
    $headers = @{
        "x-api-key"         = $env:CLAUDE_API_KEY
        "anthropic-version" = "2023-06-01"
    }
    
    $body = @{
        model      = "claude-3-5-sonnet-20241022"
        max_tokens = 4096
        messages   = @(@{ role = "user"; content = $Prompt })
    } | ConvertTo-Json
    
    Invoke-WebRequest -Uri "https://api.anthropic.com/v1/messages" `
        -Method Post -Headers $headers -Body $body
}
```

---

## Best Practices for Claude Ralph Loops

### Practice 1: Clear Role Definition
```
You are an expert code implementer following the Ralph Loop pattern.
You are methodical, test-driven, and careful about blocker escalation.
You ask for clarification when requirements are ambiguous.
```

### Practice 2: Example-Driven Prompting
Show Claude examples of good blockers and good implementations:

```
EXAMPLES OF GOOD BLOCKERS:
- "Test requires authentication, but spec doesn't clarify JWT vs. session storage"
- "Acceptance criteria says 'efficient' but doesn't define threshold"

EXAMPLES OF DESIGN CHOICES (NOT BLOCKERS):
- Choosing between bcrypt or argon2 (both valid; you decide)
- Choosing between interface vs. abstract class (both valid; you decide)
```

### Practice 3: Iterative Refinement
If Claude over-escalates on first iteration, guide it:

```
Previous blocker: "Architecture decision required: JWT vs. sessions"
Guidance: You can choose either; please pick one and explain your reasoning.
Next iteration: [Claude chooses + explains + implements]
```

### Practice 4: Cost Awareness
Monitor token usage; optimize as needed:

```bash
# Log token usage
PROMPT_TOKENS=$(echo "$response" | jq '.usage.input_tokens')
RESPONSE_TOKENS=$(echo "$response" | jq '.usage.output_tokens')
TOTAL=$((PROMPT_TOKENS + RESPONSE_TOKENS))
COST=$(echo "scale=4; $TOTAL * 0.003 / 1000000" | bc)  # Sonnet price
echo "Iteration cost: \$$COST (tokens: $TOTAL)"
```

### Practice 5: Blocker Pattern Consistency
Use `[BLOCKED]` consistently so scripts can detect them:

```
Claude response:
"[BLOCKED] Ambiguous acceptance criteria
Criterion: 'User experience is smooth'
Question: What defines 'smooth'? < 100ms? < 1s?"

Script detects: `grep "\[BLOCKED\]" response.txt` ✓
```

---

## Example Claude Ralph Loop Session

### Iteration 1: Write Test

**Prompt:**
```
You are implementing a feature following Test-Driven Development (TDD).

TASK:
Add user authentication to web service

ACCEPTANCE CRITERIA:
- Users can sign up with email and password
- Passwords are hashed using bcrypt
- Sign up fails if email already exists
- Session tokens are generated after successful login

CURRENT STATE:
No authentication code exists yet.
Test framework: Jest (npm test)
No existing auth tests

DIRECTIVE:
Write a failing test for the first acceptance criterion: "Users can sign up with email and password"

OUTPUT:
- The test code (complete and runnable)
- Expected behavior
- Where to save the file
```

**Claude Response:**
```javascript
// test/auth/signup.test.ts

import { signup } from '../../src/auth/signup';

describe('User Signup', () => {
  it('should allow users to sign up with email and password', async () => {
    const result = await signup({
      email: 'user@example.com',
      password: 'securePassword123'
    });
    
    expect(result.success).toBe(true);
    expect(result.userId).toBeDefined();
    expect(result.email).toBe('user@example.com');
  });
});
```

**Status:** Test created; run `npm test` and confirm it fails ✓

### Iteration 2: Implement Code

**Prompt:**
```
You are implementing a feature following Test-Driven Development (TDD).

FAILING TEST:
[Shows the test from iteration 1]

CURRENT STATE:
No src/auth/signup.ts exists yet.
Database: PostgreSQL with user table

DIRECTIVE:
Implement the minimum code to make the failing test pass.
Do not add more features than the test requires.

OUTPUT:
- What you will implement
- The code (complete and ready to paste)
- Suggested commit message
```

**Claude Response:**
```typescript
// src/auth/signup.ts

import crypto from 'crypto';

export async function signup(credentials: {
  email: string;
  password: string;
}) {
  const userId = crypto.randomUUID();
  
  return {
    success: true,
    userId,
    email: credentials.email
  };
}
```

**Status:** Code created; run `npm test` and confirm it passes ✓

### Iteration 3: Write Test for Criterion #2 (Password Hashing)

**Claude Response:**
```javascript
// test/auth/signup.test.ts (additional test)

it('should hash passwords using bcrypt', async () => {
  const email = 'user@example.com';
  const password = 'securePassword123';
  
  const result = await signup({ email, password });
  
  // Retrieve user from database
  const user = await getUser(result.userId);
  
  // Password should not be stored in plain text
  expect(user.passwordHash).toBeDefined();
  expect(user.passwordHash).not.toBe(password);
  
  // Hash should be bcrypt format
  expect(user.passwordHash).toMatch(/^\$2[aby]\$/);
});
```

**Status:** Test created; run `npm test` and confirm it fails ✓ (password not being hashed)

### Iteration 4: Implement Password Hashing

**Claude Response:**
```typescript
// src/auth/signup.ts (updated)

import crypto from 'crypto';
import bcrypt from 'bcrypt';

export async function signup(credentials: {
  email: string;
  password: string;
}) {
  const userId = crypto.randomUUID();
  const passwordHash = await bcrypt.hash(credentials.password, 10);
  
  // Save to database
  await db.query(
    'INSERT INTO users (id, email, password_hash) VALUES ($1, $2, $3)',
    [userId, credentials.email, passwordHash]
  );
  
  return {
    success: true,
    userId,
    email: credentials.email
  };
}
```

**Status:** Code created; run `npm test` and confirm all tests pass ✓

### Continue Until All Criteria Met...

After iterations 5-6, all criteria are met:
- ✓ Users can sign up with email and password
- ✓ Passwords are hashed using bcrypt
- ✓ Sign up fails if email already exists (needs test + implementation)
- ✓ Session tokens are generated after successful login (needs test + implementation)

**Ralph Loop Complete:** Code is ready for human review.

---

## Debugging Claude Issues

### Issue: Claude repeats previous code

**Cause:** Context window includes old implementation.  
**Solution:** Explicitly reference current state in prompt:

```
CURRENT STATE:
Previous iterations created src/auth/signup.ts, but it's incomplete.
The latest test shows: [paste failing test]
Please check if src/auth/signup.ts already handles this, or needs updates.
```

### Issue: Claude over-implements

**Cause:** Prompt is too open-ended.  
**Solution:** Add constraint:

```
CONSTRAINT:
Implement ONLY what the failing test requires.
Do NOT add:
- Additional authentication methods
- User profile endpoints
- Rate limiting
- Audit logging
```

### Issue: Claude doesn't detect blocker

**Cause:** Ambiguity not explicit.  
**Solution:** Call out the ambiguity:

```
AMBIGUITY DETECTED:
Acceptance criterion says "passwords should be 'strong'"
But doesn't define: minimum length? complexity requirements? special characters?

Is this a blocker requiring clarification? Please assess.
```

---

## See Also

- **[Ralph Loop Pattern (Core Concept)](../README.md)** — Harness-agnostic pattern definition
- **[Bash Implementation](./bash.md)** — Ralph Loop in Bash
- **[PowerShell Implementation](./powershell.md)** — Ralph Loop in PowerShell
- **[Copilot Adapter](./copilot.md)** — GitHub Copilot integration
- **[OpenCode Adapter](./opencode.md)** — OpenCode integration
