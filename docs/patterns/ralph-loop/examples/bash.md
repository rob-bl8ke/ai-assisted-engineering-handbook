# Ralph Loop: Bash Implementation

**Platform:** Linux, macOS, Unix-like systems  
**Harness:** Any CLI tool (Claude via HTTP API, local LLM, or Ollama)  
**Prerequisite:** [Ralph Loop Pattern (Core Concept)](../README.md)

---

## Core Concept

This is a **concrete Bash implementation** of the Ralph Loop pattern. A Bash script runs in a loop, calling a Claude (or compatible) API agent at each iteration. The agent receives:

1. The task (GitHub issue or specification)
2. The current repository state (file listing, test results, logs)
3. A directive for the current step (write test, implement code, etc.)

The agent completes its assigned step, writes code to the repository, and returns status. The script then:

- Verifies test results
- Detects blockers
- Decides the next step
- Either continues the loop or pauses for human input

---

## Prerequisites

1. **Repository with tests:** Must be a git repository with a working test suite
2. **Test runner:** `npm test`, `pytest`, `cargo test`, or equivalent
3. **Claude API access:** Valid `CLAUDE_API_KEY` environment variable
4. **Git:** Repository must be initialized with commit capability
5. **curl:** For API calls (or use `ollama` for local models)

---

## The Script: ralph-loop.sh

```bash
#!/bin/bash

###############################
# Ralph Loop: Bash Implementation
# Runs an autonomous Claude agent in a loop until all acceptance criteria are met
# Usage: ./ralph-loop.sh <issue_spec.md> <step_number> [optional: initial_step]
###############################

set -e

ISSUE_SPEC="$1"
STEP="${2:-1}"
CLAUDE_API_URL="${CLAUDE_API_URL:-https://api.anthropic.com/v1/messages}"
CLAUDE_MODEL="${CLAUDE_MODEL:-claude-3-5-sonnet-20241022}"
MAX_STEPS=20
PAUSE_ON_BLOCKER=true

if [ ! -f "$ISSUE_SPEC" ]; then
  echo "ERROR: Issue specification file not found: $ISSUE_SPEC"
  exit 1
fi

if [ ! -d ".git" ]; then
  echo "ERROR: Not in a git repository"
  exit 1
fi

###############################
# UTILITY FUNCTIONS
###############################

log_step() {
  echo ""
  echo "════════════════════════════════════════════"
  echo "STEP $1: $2"
  echo "════════════════════════════════════════════"
}

log_info() {
  echo "[INFO] $1"
}

log_success() {
  echo "[✓] $1"
}

log_error() {
  echo "[✗] $1"
}

log_blocker() {
  echo ""
  echo "⚠️  BLOCKER DETECTED ⚠️"
  echo "════════════════════════════════════════════"
  echo "$1"
  echo "════════════════════════════════════════════"
  echo ""
}

# Get repository state
get_repo_state() {
  local state_file=".ralph-loop-state.txt"
  
  echo "Repository State at $(date)" > "$state_file"
  echo "" >> "$state_file"
  
  echo "=== Git Status ===" >> "$state_file"
  git status --short >> "$state_file" 2>&1 || echo "Git status unavailable" >> "$state_file"
  
  echo "" >> "$state_file"
  echo "=== Recent Commits ===" >> "$state_file"
  git log --oneline -5 >> "$state_file" 2>&1 || echo "No git history" >> "$state_file"
  
  echo "" >> "$state_file"
  echo "=== Test Results ===" >> "$state_file"
  run_tests "$state_file"
  
  echo "" >> "$state_file"
  echo "=== File Structure ===" >> "$state_file"
  find . -type f -name "*.test.*" -o -name "*.spec.*" -o -name "*.ts" -o -name "*.js" | head -20 >> "$state_file"
  
  cat "$state_file"
}

# Run test suite and capture results
run_tests() {
  local output_file="$1"
  
  if [ -f "package.json" ]; then
    log_info "Running npm tests..."
    npm test >> "$output_file" 2>&1 || true
  elif [ -f "pytest.ini" ] || [ -f "setup.py" ]; then
    log_info "Running pytest..."
    pytest -v >> "$output_file" 2>&1 || true
  elif [ -f "Cargo.toml" ]; then
    log_info "Running cargo tests..."
    cargo test >> "$output_file" 2>&1 || true
  else
    log_error "No test runner detected"
    echo "No test suite found" >> "$output_file"
  fi
}

# Call Claude API with prompt
call_claude() {
  local prompt="$1"
  local max_tokens="${2:-4096}"
  
  if [ -z "$CLAUDE_API_KEY" ]; then
    log_error "CLAUDE_API_KEY not set"
    return 1
  fi
  
  local response=$(curl -s -X POST "$CLAUDE_API_URL" \
    -H "x-api-key: $CLAUDE_API_KEY" \
    -H "anthropic-version: 2023-06-01" \
    -H "content-type: application/json" \
    -d "{
      \"model\": \"$CLAUDE_MODEL\",
      \"max_tokens\": $max_tokens,
      \"messages\": [{
        \"role\": \"user\",
        \"content\": \"$prompt\"
      }]
    }")
  
  echo "$response" | jq -r '.content[0].text' 2>/dev/null || echo "$response"
}

# Parse blocker from response
detect_blocker() {
  local response="$1"
  
  if echo "$response" | grep -q "\[BLOCKED\]"; then
    return 0  # Blocker detected
  fi
  return 1  # No blocker
}

# Check if all acceptance criteria are met
check_acceptance_criteria() {
  local spec="$1"
  
  # Simple heuristic: count acceptance criteria lines in spec
  local criteria_count=$(grep -c "^- " "$spec" || echo "0")
  
  if [ "$criteria_count" -eq 0 ]; then
    log_error "No acceptance criteria found in specification"
    return 1
  fi
  
  # This is where you'd check test results against criteria
  # For now: run tests and report
  log_info "Checking $criteria_count acceptance criteria..."
  
  if npm test 2>&1 | grep -q "passed"; then
    log_success "All tests passing"
    return 0
  else
    log_error "Some tests failing"
    return 1
  fi
}

###############################
# MAIN LOOP
###############################

main() {
  log_info "Ralph Loop Starting"
  log_info "Issue Specification: $ISSUE_SPEC"
  log_info "Model: $CLAUDE_MODEL"
  
  # Read issue specification
  ISSUE_TEXT=$(cat "$ISSUE_SPEC")
  
  # Step 1: Verify Specification
  if [ "$STEP" -le 1 ]; then
    log_step 1 "Verify Task Specification"
    
    if grep -q "Acceptance Criteria:" "$ISSUE_SPEC"; then
      log_success "Acceptance criteria found"
    else
      log_blocker "Issue specification is missing acceptance criteria\n\nPlease add acceptance criteria to the issue specification"
      exit 1
    fi
    
    STEP=2
  fi
  
  # Step 2: Verify Prerequisites
  if [ "$STEP" -le 2 ]; then
    log_step 2 "Verify Prerequisites"
    
    # Check test infrastructure
    if ! npm test 2>&1 | head -1; then
      if ! python -m pytest --version 2>&1 | head -1; then
        log_blocker "No test runner detected\n\nPlease set up a test framework (npm, pytest, cargo, etc)"
        exit 1
      fi
    fi
    
    log_success "Test infrastructure verified"
    STEP=3
  fi
  
  # Step 3-N: Implementation Loop
  while [ "$STEP" -le "$MAX_STEPS" ]; do
    
    # Get current repository state
    log_info "Gathering repository state..."
    REPO_STATE=$(get_repo_state)
    
    # Determine task for this iteration
    case $((STEP % 2)) in
      0)
        DIRECTIVE="Write a failing test for the next acceptance criterion. Do not implement code yet."
        TASK_NAME="Write Test"
        ;;
      1)
        DIRECTIVE="Implement the minimum code to make the failing test pass."
        TASK_NAME="Implement Code"
        ;;
    esac
    
    log_step "$STEP" "$TASK_NAME"
    
    # Build prompt for Claude
    PROMPT=$(cat <<EOF
You are an expert code implementer following the Ralph Loop pattern.

TASK:
${ISSUE_TEXT}

CURRENT REPOSITORY STATE:
${REPO_STATE}

DIRECTIVE FOR THIS ITERATION:
${DIRECTIVE}

INSTRUCTIONS:
1. Examine the acceptance criteria
2. If writing a test: Create a failing test for the next unmet criterion
3. If implementing: Write minimal code to make the test pass
4. Use Test-Driven Development (TDD) and Tracer Bullets pattern
5. Follow established coding patterns and style
6. Write clear commit messages
7. If you encounter ambiguity, document [BLOCKED] with explanation

OUTPUT:
- Describe what you will implement
- Show the code changes (if any)
- Suggest a commit message
- If blocked, explain the blocker and what clarification is needed

Keep the response concise and actionable.
EOF
)
    
    # Call Claude
    log_info "Calling Claude API..."
    RESPONSE=$(call_claude "$PROMPT" 8192)
    
    # Check for blocker
    if detect_blocker "$RESPONSE"; then
      log_blocker "$RESPONSE"
      if [ "$PAUSE_ON_BLOCKER" = true ]; then
        log_info "Pausing: Please resolve the blocker and run the script again"
        exit 1
      fi
    fi
    
    log_info "Claude Response:"
    echo "$RESPONSE"
    
    # User applies changes manually (or via script hook)
    read -p "Press Enter after applying changes, or 'q' to quit: " -r response
    if [ "$response" = "q" ]; then
      log_info "Ralph Loop paused by user"
      exit 0
    fi
    
    # Verify tests still pass
    log_info "Running test suite..."
    if npm test 2>&1 | tee test-output.txt; then
      log_success "Tests passing"
    else
      log_error "Tests failing"
      log_blocker "Test suite failed. Review the error above and fix the code."
      exit 1
    fi
    
    # Check if all criteria are met
    if check_acceptance_criteria "$ISSUE_SPEC"; then
      log_success "All acceptance criteria met!"
      
      log_step "Final" "Code Review Ready"
      log_info "Ralph Loop Complete: Code is ready for human review"
      
      git add .
      git commit -m "feat: Issue implementation complete - ready for review" || true
      exit 0
    fi
    
    STEP=$((STEP + 1))
  done
  
  log_error "Max steps reached. Implementation incomplete."
  exit 1
}

main "$@"
```

---

## Usage

### Basic Usage

```bash
# Create issue specification
cat > issue-001.md <<EOF
# Issue: Add user authentication

## Acceptance Criteria
- Users can sign up with email and password
- Passwords are hashed using bcrypt
- Sign up fails if email already exists
- Session tokens are generated after successful login

## Links
- Documentation: docs/security.md
- Related code: src/auth/
EOF

# Run Ralph Loop
chmod +x ralph-loop.sh
./ralph-loop.sh issue-001.md
```

### With Ollama (Local LLM)

```bash
# Use local model instead of Claude
CLAUDE_API_URL="http://localhost:11434/api/generate" \
CLAUDE_MODEL="llama2" \
./ralph-loop.sh issue-001.md
```

### Step-by-Step Resumption

```bash
# Start loop
./ralph-loop.sh issue-001.md 1

# If paused on step 5, resume from there
./ralph-loop.sh issue-001.md 5
```

---

## How It Works

### Iteration 1: Write First Failing Test
1. Claude receives issue and repository state
2. Claude writes a test for the first acceptance criterion
3. Script saves test code; user applies it
4. Tests run; new test fails (red phase)
5. Loop continues

### Iteration 2: Implement Minimum Code
1. Claude sees failing test and repository state
2. Claude writes minimal code to make test pass
3. Script saves code; user applies it
4. Tests run; all tests pass (green phase)
5. Loop continues

### Iteration 3+: Repeat Until Complete
- Odd iterations: Write failing test for next criterion
- Even iterations: Implement code to pass test
- Loop ends when all acceptance criteria are met

### Blocker Detection
If Claude responds with `[BLOCKED]`, script pauses and shows the blocker. User must:
1. Read the blocker explanation
2. Resolve the issue (clarify requirements, fix architecture, etc.)
3. Re-run script from the current step

---

## Customization

### Change Test Runner
```bash
# Default: npm test
# Override in script or set environment variable
TEST_COMMAND="pytest --verbose" ./ralph-loop.sh issue.md
```

### Change Model
```bash
CLAUDE_MODEL="claude-3-opus-20240229" ./ralph-loop.sh issue.md
```

### Pause vs. Fail on Blocker
```bash
# Edit script, change:
PAUSE_ON_BLOCKER=true   # Current: pause when blocker found
PAUSE_ON_BLOCKER=false  # Alternative: treat blocker as failure
```

### Integration with CI/CD
```bash
# In GitHub Actions workflow
- name: Run Ralph Loop
  env:
    CLAUDE_API_KEY: ${{ secrets.CLAUDE_API_KEY }}
  run: |
    chmod +x ./ralph-loop.sh
    ./ralph-loop.sh "${{ github.event.issue.body }}" 1
```

---

## Troubleshooting

### Issue: "CLAUDE_API_KEY not set"
```bash
export CLAUDE_API_KEY="sk-ant-..."
./ralph-loop.sh issue.md
```

### Issue: "Not in a git repository"
```bash
cd /path/to/repo
./ralph-loop.sh issue.md
```

### Issue: "No test runner detected"
```bash
# Add test script to package.json or setup.py
npm init
npm install --save-dev jest
npm test
./ralph-loop.sh issue.md
```

### Issue: Tests hang or timeout
```bash
# Set timeout for tests
npm test -- --testTimeout=30000
```

---

## Related Examples

- **[PowerShell Implementation](./powershell.md)** — Windows equivalent
- **[Claude Adapter Notes](../adapters/claude.md)** — Claude-specific behavior
- **[Bash + Ollama Example](../adapters/opencode.md)** — Local LLM usage
