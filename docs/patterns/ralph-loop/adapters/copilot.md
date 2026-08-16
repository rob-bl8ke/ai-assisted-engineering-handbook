# Ralph Loop: GitHub Copilot Adapter

**Harness:** GitHub Copilot (VS Code, JetBrains, Visual Studio)  
**Prerequisite:** [Ralph Loop Pattern (Core Concept)](../README.md)

---

## Overview

This adapter documents GitHub Copilot-specific behavior and integration for Ralph Loops. Copilot is well-suited for Ralph Loops when used in an IDE because:

1. **Deep IDE Integration:** Copilot understands repository structure, open files, and project context
2. **In-Editor Suggestions:** Can suggest code, tests, and refactorings in the flow
3. **Chat Interface:** Copilot Chat allows multi-turn conversations within the IDE
4. **Workspace Awareness:** Understands current file, selection, and open tabs
5. **Quick Feedback:** Instant code completion and chat responses

---

## Copilot Setup

### Prerequisites

1. **GitHub Copilot Subscription:** $10/month individual or $19/month business
2. **VS Code Extension:** "GitHub Copilot" + "GitHub Copilot Chat" from VS Code Marketplace
3. **GitHub Account:** Linked to VS Code
4. **Repository Access:** Open the repository folder in VS Code

### Enabling Chat in VS Code

1. Open VS Code
2. Install "GitHub Copilot Chat" extension
3. Open Command Palette (`Cmd/Ctrl + Shift + P`)
4. Search for "Copilot Chat: Focus on Chat View"
5. Chat panel opens on the side

---

## Ralph Loop Workflow in VS Code

### Setup Phase: Create Task Specification

**File: issue-001.md**
```markdown
# Issue: Add user authentication

## Acceptance Criteria
- Users can sign up with email and password
- Passwords are hashed using bcrypt
- Sign up fails if email already exists
- Session tokens are generated after successful login

## Links
- Documentation: docs/security.md
- Related code: src/auth/
```

### Phase 1: Write Failing Test

**In VS Code Chat:**
```
@copilot
Read the issue in issue-001.md.
Write a failing test for the first acceptance criterion using Jest.
Save it in test/auth/signup.test.ts
```

**Copilot Response:**
- Suggests test file structure
- Generates test code (may provide inline suggestions)
- Recommends file location

**You:**
1. Review test suggestion
2. Create `test/auth/signup.test.ts`
3. Paste test code
4. Run: `npm test` (confirm test fails)
5. Commit: `git commit -am "tests: add signup acceptance test"`

### Phase 2: Implement Code

**In VS Code Chat:**
```
@copilot
The test in test/auth/signup.test.ts is failing.
Implement the minimum code in src/auth/signup.ts to make the test pass.
Use bcrypt for passwords (will add in next iteration).
Just make the test pass for now.
```

**Copilot Response:**
- Reviews failing test
- Suggests implementation code
- Highlights what's needed vs. what's out of scope

**You:**
1. Review implementation suggestion
2. Apply code to `src/auth/signup.ts`
3. Run: `npm test` (confirm test passes)
4. Commit: `git commit -am "feat: basic signup implementation"`

### Phase 3+: Repeat for Next Criteria

Continue the cycle: write test → implement code → iterate.

---

## Copilot Chat Commands for Ralph Loop

### Command 1: Initialize Issue

**Chat Input:**
```
@copilot
I'm starting a Ralph Loop implementation session.

Here's the issue:
[paste issue specification]

I'll follow Test-Driven Development:
1. Write a failing test
2. Implement minimum code
3. Repeat for each acceptance criterion

Please confirm you understand, then help me write the first test.
```

**Copilot Response:**
Acknowledges the pattern, offers to help with test writing.

### Command 2: Write Test Prompt

**Chat Input:**
```
@copilot
Write a Jest test for this acceptance criterion:
"Users can sign up with email and password"

Use this structure:
- Test file: test/auth/signup.test.ts
- Test should verify email and password are accepted
- Test should verify a user is created with those credentials
- Test should fail (we haven't implemented signup yet)

Generate the complete test code.
```

### Command 3: Implement Code Prompt

**Chat Input:**
```
@copilot
The test at test/auth/signup.test.ts is failing.

Write the minimum code to make it pass:
- File: src/auth/signup.ts
- Export a function that accepts email and password
- Return a user object with userId and email
- Don't worry about hashing passwords yet (next test)
- Don't add extra features

Generate the complete implementation.
```

### Command 4: Blocker Diagnosis

**Chat Input:**
```
@copilot
I'm stuck. The test is failing with this error:
[paste error message]

The acceptance criteria requires:
[paste criterion]

What's the blocker? Do I need:
1. More code changes?
2. Clarification from product owner?
3. A different approach?
4. Infrastructure setup?

Diagnosis and recommendation?
```

### Command 5: Edge Cases

**Chat Input:**
```
@copilot
The basic implementation is working.
Before moving to the next criterion, are there edge cases I should test?

Current implementation handles:
- Storing email and password
- Basic signup flow

Potential edge cases:
- Duplicate emails?
- Invalid email format?
- Empty passwords?
- Very long inputs?

Which are critical for acceptance criteria?
```

---

## Copilot-Specific Features for Ralph Loop

### Feature 1: Inline Code Suggestions

**Scenario:** You type the test file header, Copilot auto-completes

```typescript
// test/auth/signup.test.ts
import { signup } from '../../src/auth/signup';

describe('User Signup', () => {
  it('should allow users to sign up with email and password', async () => {
    // Copilot suggests the rest of the test
    const result = await signup({
      // ... autocomplete continues
    });
  });
});
```

**Activation:** Start typing, press Tab to accept Copilot's suggestion.

### Feature 2: Context-Aware Suggestions

Copilot sees:
- Test file structure (Jest)
- Existing code patterns (async/await, error handling)
- Project dependencies (bcrypt, database client)

**Result:** Suggestions are consistent with existing code.

### Feature 3: Refactoring Suggestions

**Scenario:** You've implemented basic signup; Copilot suggests cleanup

```typescript
// Original (works, but messy)
export async function signup(creds) {
  // ... lots of inline logic
}

// Copilot suggests:
export async function signup(credentials: SignupRequest): Promise<SignupResponse> {
  // ... refactored with types
}
```

**Activation:** Highlight code block → Right-click → "Ask Copilot" → request refactoring.

### Feature 4: Documentation Generation

**Scenario:** Copilot generates JSDoc for your implementation

```typescript
/**
 * Create a new user account with email and password
 * @param credentials - Object with email and password
 * @returns Promise resolving to new user object with userId and email
 * @throws Error if email already exists
 */
export async function signup(credentials: SignupRequest): Promise<SignupResponse> {
  // ...
}
```

---

## Best Practices for Copilot Ralph Loops

### Practice 1: Use Chat for Direction, IDE for Implementation

**Chat Role:**
- Ask for test/code suggestions
- Get blocker diagnosis
- Clarify requirements

**IDE Role:**
- Apply suggestions to files
- Run tests (`npm test`)
- Commit changes (`git commit`)

### Practice 2: Reference Current State

In Chat, tell Copilot what you see:

```
@copilot
Current state:
- Test file: test/auth/signup.test.ts (exists, failing)
- Implementation: src/auth/signup.ts (doesn't exist yet)

Next: Write src/auth/signup.ts to make the test pass.
```

Copilot learns from this context.

### Practice 3: Accept Partial Suggestions

Copilot's inline suggestions often need tweaking:

```
Copilot suggests:
export const signup = async (email, password) => {
  // ... reasonable implementation
  return { success, userId };
};

You can:
- Accept as-is (Tab)
- Accept partially (edit + modify)
- Reject and continue typing
```

### Practice 4: Use @mentions in Chat

Target Copilot's attention:

```
@copilot
Look at @test/auth/signup.test.ts
What does the failing test expect?

Then, in @src/, how should I implement it?
```

Copilot focuses on mentioned files.

### Practice 5: Explain Your Choices

Help Copilot understand your constraints:

```
@copilot
I need to implement user signup.
Constraints:
- Use TypeScript (strict mode)
- Use bcrypt for passwords
- Use PostgreSQL (not in-memory)
- Follow error-first async pattern

Given these constraints, suggest the test structure.
```

---

## Copilot vs. Claude for Ralph Loop

| Aspect | Copilot | Claude |
|--------|---------|--------|
| **UI** | IDE-integrated | Separate chat window or CLI |
| **Context** | Open files + folder structure | Full repository state (user provides) |
| **Speed** | Inline instant suggestions | API latency (~2s per call) |
| **Depth** | Good at code; less reasoning | Excellent reasoning; code suggestions |
| **Cost** | $10/month subscription | Pay-per-token (~$0.01 per iteration) |
| **Best for** | Interactive TDD in IDE | Autonomous/looped implementation |
| **Requires** | VS Code + GitHub account | API key + script |

**Copilot Ralph Loop:** Human runs loop, Copilot assists at each step.  
**Claude Ralph Loop:** Script runs loop autonomously, pauses on blockers.

---

## Common Copilot Limitations

### Limitation 1: File Size Context
Copilot doesn't include entire codebase; only open files and nearby context.

**Mitigation:**
- Keep relevant files open during Ralph Loop
- Explicitly mention file relationships in Chat
- Use `@file` mentions to focus attention

### Limitation 2: Test Result Integration
Copilot can't automatically see test results; you must report them.

**Workaround:**
```
@copilot
I ran npm test. Result:
✓ Users can sign up (test passes)
✗ Passwords hashed (test fails, error: crypto module not imported)

What's next?
```

### Limitation 3: Repeated Code Patterns
Copilot sometimes repeats code structure even when you've asked for variation.

**Workaround:**
- Explicitly reject pattern: "Don't use this structure, use [alternative] instead"
- Show example of desired pattern
- Ask Copilot to explain differences

### Limitation 4: Context Reset per Chat
Each new Chat message restarts context; no persistent state.

**Workaround:**
- Keep Chat history; reference previous messages
- Explicitly remind Copilot of completed work
- Use file mentions (`@file`) to maintain context

---

## Ralph Loop with Copilot Chat: Step-by-Step

### Setup
1. Open repository in VS Code
2. Create issue specification file (issue-001.md)
3. Open Copilot Chat (`Cmd/Ctrl + Shift + I`)

### Iteration 1: Write Test
```
Chat Input:
@copilot help me write a failing test for this criterion:
"Users can sign up with email and password"

Use Jest, save to test/auth/signup.test.ts
```

Result: Copilot suggests test code; you create file and apply it.

### Iteration 2: Implement Code
```
Chat Input:
@copilot the test in @test/auth/signup.test.ts is failing.
Write the minimum code to src/auth/signup.ts to make it pass.
```

Result: Copilot suggests implementation; you create file and apply it.

### Iteration 3: Next Criterion
```
Chat Input:
@copilot next criterion is "Passwords are hashed using bcrypt"
Write a test that verifies password hashing.
The implementation should use bcrypt.hash()
```

Result: Copilot suggests test; you apply it.

### Iteration 4: Implement Hashing
```
Chat Input:
@copilot implement password hashing in @src/auth/signup.ts
The test expects passwords to be bcrypt-hashed.
Use bcrypt.hash() with 10 rounds.
```

Result: Copilot suggests implementation; you apply it.

### Continue...
Repeat iterations until all acceptance criteria are met.

---

## Troubleshooting Copilot Issues

### Issue: Copilot suggests outdated patterns

**Cause:** Training data may include older conventions.  
**Solution:** Explicitly request modern patterns:

```
@copilot
Use modern TypeScript (2024):
- Strict mode enabled
- Use async/await (not promises)
- Use template literals for strings
- Use arrow functions

Then suggest the test code.
```

### Issue: Suggestions are incomplete

**Cause:** Copilot stopped mid-way through code.  
**Solution:** Ask for continuation:

```
@copilot
Continue from where you left off. I need the full implementation.
```

Or manually complete; Copilot adapts to your continuation.

### Issue: Copilot forgets acceptance criteria

**Cause:** Context reset per message.  
**Solution:** Include criteria in each prompt:

```
@copilot
Reminder - full acceptance criteria:
1. Users can sign up
2. Passwords are hashed
3. ...

Now, write the next test for criterion #2.
```

### Issue: Chat history is too long

**Cause:** VS Code Copilot Chat has a message limit.  
**Solution:** Start new chat session:

1. Close current Chat
2. Open new Chat (Copilot Chat: New Chat)
3. Include summary: "I've implemented criteria 1-3, now working on criteria 4"

---

## Integration with Ralph Loop Scripts

Use Copilot Chat *alongside* automated scripts:

**Bash Ralph Loop Script:**
- Runs autonomously using Claude API
- Pauses on blockers

**Copilot Chat Interface:**
- Used for interactive issue exploration
- Used for debugging or alternative approaches
- Used for learning how Claude decided something

**Example Workflow:**
```bash
# Start autonomous Ralph Loop with Claude
./ralph-loop.sh issue-001.md

# If it pauses on [BLOCKED], use Copilot Chat to diagnose:
@copilot
The automated Ralph Loop paused with this blocker:
[paste blocker]

Should this be escalated to human, or can I resolve it?
```

---

## See Also

- **[Ralph Loop Pattern (Core Concept)](../README.md)** — Harness-agnostic pattern definition
- **[Claude Adapter](./claude.md)** — Claude integration for autonomous Ralph Loops
- **[OpenCode Adapter](./opencode.md)** — OpenCode integration
- **[Bash Implementation](../examples/bash.md)** — Scripted Ralph Loop
- **[PowerShell Implementation](../examples/powershell.md)** — Windows scripted Ralph Loop
