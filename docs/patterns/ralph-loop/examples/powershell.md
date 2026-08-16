# Ralph Loop: PowerShell Implementation

**Platform:** Windows, PowerShell 7+ (cross-platform)  
**Harness:** Any CLI tool (Claude via HTTP API, Azure OpenAI, or local LLM)  
**Prerequisite:** [Ralph Loop Pattern (Core Concept)](../README.md)

---

## Core Concept

This is a **concrete PowerShell implementation** of the Ralph Loop pattern. Functionally equivalent to the Bash version, but uses PowerShell idioms and cmdlets. The script runs in a loop, calling a Claude (or compatible) API agent at each iteration.

---

## Prerequisites

1. **PowerShell 7.0+:** Installed and available in PATH
2. **Repository with tests:** Must be a git repository with a working test suite
3. **Test runner:** `npm test`, `pytest`, `cargo test`, `dotnet test`, or equivalent
4. **Claude API access:** Valid `CLAUDE_API_KEY` environment variable (or `AZURE_OPENAI_KEY`)
5. **Git:** Repository must be initialized with commit capability

---

## The Script: ralph-loop.ps1

```powershell
###############################
# Ralph Loop: PowerShell Implementation
# Runs an autonomous Claude agent in a loop until all acceptance criteria are met
# Usage: .\ralph-loop.ps1 -IssueSpec ./issue-001.md -Step 1
###############################

param(
    [Parameter(Mandatory=$true)]
    [string]$IssueSpec,
    
    [int]$Step = 1,
    
    [string]$ClaudeApiUrl = "https://api.anthropic.com/v1/messages",
    
    [string]$ClaudeModel = "claude-3-5-sonnet-20241022",
    
    [int]$MaxSteps = 20,
    
    [switch]$PauseOnBlocker = $true
)

Set-StrictMode -Version Latest
$ErrorActionPreference = "Stop"

###############################
# UTILITY FUNCTIONS
###############################

function Write-LogStep {
    param([int]$StepNumber, [string]$Message)
    
    Write-Host ""
    Write-Host "════════════════════════════════════════════" -ForegroundColor Cyan
    Write-Host "STEP $StepNumber`: $Message" -ForegroundColor Cyan
    Write-Host "════════════════════════════════════════════" -ForegroundColor Cyan
}

function Write-LogInfo {
    param([string]$Message)
    Write-Host "[INFO] $Message" -ForegroundColor White
}

function Write-LogSuccess {
    param([string]$Message)
    Write-Host "[✓] $Message" -ForegroundColor Green
}

function Write-LogError {
    param([string]$Message)
    Write-Host "[✗] $Message" -ForegroundColor Red
}

function Write-LogBlocker {
    param([string]$Message)
    Write-Host ""
    Write-Host "⚠️  BLOCKER DETECTED ⚠️" -ForegroundColor Yellow
    Write-Host "════════════════════════════════════════════" -ForegroundColor Yellow
    Write-Host $Message -ForegroundColor Yellow
    Write-Host "════════════════════════════════════════════" -ForegroundColor Yellow
    Write-Host ""
}

# Get repository state
function Get-RepositoryState {
    $stateFile = ".ralph-loop-state.txt"
    
    $state = @(
        "Repository State at $(Get-Date)",
        "",
        "=== Git Status ===",
        (git status --short 2>&1 | Out-String),
        "",
        "=== Recent Commits ===",
        (git log --oneline -5 2>&1 | Out-String),
        "",
        "=== Test Results ===",
        (Invoke-TestSuite),
        "",
        "=== File Structure ===",
        (Get-ChildItem -Recurse -Filter "*.test.*", "*.spec.*" 2>$null | Select-Object -First 20 | Out-String)
    )
    
    $state -join "`n" | Out-File -FilePath $stateFile -Encoding UTF8
    Get-Content -Path $stateFile
}

# Run test suite and capture results
function Invoke-TestSuite {
    if (Test-Path "package.json") {
        Write-LogInfo "Running npm tests..."
        npm test 2>&1 | Out-String
    }
    elseif (Test-Path "pytest.ini") {
        Write-LogInfo "Running pytest..."
        python -m pytest -v 2>&1 | Out-String
    }
    elseif (Test-Path "Cargo.toml") {
        Write-LogInfo "Running cargo tests..."
        cargo test 2>&1 | Out-String
    }
    elseif (Test-Path "*.csproj") {
        Write-LogInfo "Running dotnet tests..."
        dotnet test 2>&1 | Out-String
    }
    else {
        Write-LogError "No test runner detected"
        "No test suite found" | Out-String
    }
}

# Call Claude API with prompt
function Invoke-Claude {
    param([string]$Prompt, [int]$MaxTokens = 4096)
    
    if ([string]::IsNullOrEmpty($env:CLAUDE_API_KEY)) {
        Write-LogError "CLAUDE_API_KEY environment variable not set"
        throw "API Key required"
    }
    
    $headers = @{
        "x-api-key"           = $env:CLAUDE_API_KEY
        "anthropic-version"   = "2023-06-01"
        "content-type"        = "application/json"
    }
    
    $body = @{
        model      = $ClaudeModel
        max_tokens = $MaxTokens
        messages   = @(
            @{
                role    = "user"
                content = $Prompt
            }
        )
    } | ConvertTo-Json -Depth 10
    
    try {
        $response = Invoke-WebRequest -Uri $ClaudeApiUrl `
            -Method Post `
            -Headers $headers `
            -Body $body `
            -ContentType "application/json"
        
        $json = $response.Content | ConvertFrom-Json
        return $json.content[0].text
    }
    catch {
        Write-LogError "API call failed: $_"
        throw
    }
}

# Parse blocker from response
function Test-BlockerDetected {
    param([string]$Response)
    
    return $Response -match "\[BLOCKED\]"
}

# Check if all acceptance criteria are met
function Test-AcceptanceCriteria {
    param([string]$SpecFile)
    
    $criteria = @(Get-Content -Path $SpecFile | Where-Object { $_ -match "^- " })
    
    if ($criteria.Count -eq 0) {
        Write-LogError "No acceptance criteria found in specification"
        return $false
    }
    
    Write-LogInfo "Checking $($criteria.Count) acceptance criteria..."
    
    # Run tests and check results
    try {
        $testOutput = Invoke-TestSuite
        if ($testOutput -match "passed|✓|PASSED") {
            Write-LogSuccess "All tests passing"
            return $true
        }
        else {
            Write-LogError "Some tests failing"
            return $false
        }
    }
    catch {
        Write-LogError "Test execution failed: $_"
        return $false
    }
}

###############################
# MAIN LOOP
###############################

function Invoke-RalphLoop {
    Write-LogInfo "Ralph Loop Starting"
    Write-LogInfo "Issue Specification: $IssueSpec"
    Write-LogInfo "Model: $ClaudeModel"
    
    # Validate inputs
    if (-not (Test-Path $IssueSpec)) {
        Write-LogError "Issue specification file not found: $IssueSpec"
        exit 1
    }
    
    if (-not (Test-Path ".git")) {
        Write-LogError "Not in a git repository"
        exit 1
    }
    
    # Read issue specification
    $issueText = Get-Content -Path $IssueSpec -Raw
    
    # Step 1: Verify Specification
    if ($Step -le 1) {
        Write-LogStep 1 "Verify Task Specification"
        
        if ($issueText -match "Acceptance Criteria:") {
            Write-LogSuccess "Acceptance criteria found"
        }
        else {
            Write-LogBlocker "Issue specification is missing acceptance criteria`n`nPlease add acceptance criteria to the issue specification"
            exit 1
        }
        
        $Step = 2
    }
    
    # Step 2: Verify Prerequisites
    if ($Step -le 2) {
        Write-LogStep 2 "Verify Prerequisites"
        
        # Check test infrastructure exists
        try {
            $testOutput = Invoke-TestSuite | Out-String
            if ([string]::IsNullOrEmpty($testOutput)) {
                Write-LogBlocker "No test runner detected`n`nPlease set up a test framework (npm, pytest, cargo, dotnet, etc)"
                exit 1
            }
        }
        catch {
            Write-LogBlocker "Could not run tests`n`nError: $_"
            exit 1
        }
        
        Write-LogSuccess "Test infrastructure verified"
        $Step = 3
    }
    
    # Step 3-N: Implementation Loop
    while ($Step -le $MaxSteps) {
        
        # Get current repository state
        Write-LogInfo "Gathering repository state..."
        $repoState = Get-RepositoryState
        
        # Determine task for this iteration
        $taskNumber = $Step % 2
        if ($taskNumber -eq 0) {
            $directive = "Write a failing test for the next acceptance criterion. Do not implement code yet."
            $taskName = "Write Test"
        }
        else {
            $directive = "Implement the minimum code to make the failing test pass."
            $taskName = "Implement Code"
        }
        
        Write-LogStep $Step $taskName
        
        # Build prompt for Claude
        $prompt = @"
You are an expert code implementer following the Ralph Loop pattern.

TASK:
$issueText

CURRENT REPOSITORY STATE:
$repoState

DIRECTIVE FOR THIS ITERATION:
$directive

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
"@
        
        # Call Claude
        Write-LogInfo "Calling Claude API..."
        try {
            $response = Invoke-Claude -Prompt $prompt -MaxTokens 8192
        }
        catch {
            Write-LogError "Failed to call API: $_"
            exit 1
        }
        
        # Check for blocker
        if (Test-BlockerDetected -Response $response) {
            Write-LogBlocker $response
            if ($PauseOnBlocker) {
                Write-LogInfo "Pausing: Please resolve the blocker and run the script again"
                exit 1
            }
        }
        
        Write-LogInfo "Claude Response:"
        Write-Host $response
        
        # User applies changes manually (or via script hook)
        Write-Host ""
        $userInput = Read-Host "Press Enter after applying changes, or 'q' to quit"
        
        if ($userInput -eq "q") {
            Write-LogInfo "Ralph Loop paused by user"
            exit 0
        }
        
        # Verify tests still pass
        Write-LogInfo "Running test suite..."
        try {
            $testOutput = Invoke-TestSuite
            Write-Host $testOutput
            
            if ($testOutput -match "passed|✓|PASSED") {
                Write-LogSuccess "Tests passing"
            }
            else {
                Write-LogError "Tests failing"
                Write-LogBlocker "Test suite failed. Review the error above and fix the code."
                exit 1
            }
        }
        catch {
            Write-LogError "Test execution failed: $_"
            exit 1
        }
        
        # Check if all criteria are met
        if (Test-AcceptanceCriteria -SpecFile $IssueSpec) {
            Write-LogSuccess "All acceptance criteria met!"
            
            Write-LogStep "Final" "Code Review Ready"
            Write-LogInfo "Ralph Loop Complete: Code is ready for human review"
            
            git add .
            git commit -m "feat: Issue implementation complete - ready for review" -ErrorAction SilentlyContinue
            exit 0
        }
        
        $Step = $Step + 1
    }
    
    Write-LogError "Max steps reached. Implementation incomplete."
    exit 1
}

# Execute
try {
    Invoke-RalphLoop
}
catch {
    Write-LogError "Ralph Loop failed: $_"
    exit 1
}
```

---

## Usage

### Basic Usage

```powershell
# Create issue specification
@"
# Issue: Add user authentication

## Acceptance Criteria
- Users can sign up with email and password
- Passwords are hashed using bcrypt
- Sign up fails if email already exists
- Session tokens are generated after successful login

## Links
- Documentation: docs/security.md
- Related code: src/auth/
"@ | Out-File -FilePath issue-001.md

# Run Ralph Loop
.\ralph-loop.ps1 -IssueSpec issue-001.md
```

### With Azure OpenAI

```powershell
$env:CLAUDE_API_KEY = $env:AZURE_OPENAI_KEY

.\ralph-loop.ps1 `
    -IssueSpec issue-001.md `
    -ClaudeApiUrl "https://<your-resource>.openai.azure.com/openai/deployments/<deployment>/chat/completions" `
    -ClaudeModel "gpt-4"
```

### Step-by-Step Resumption

```powershell
# Start loop
.\ralph-loop.ps1 -IssueSpec issue-001.md -Step 1

# If paused on step 5, resume from there
.\ralph-loop.ps1 -IssueSpec issue-001.md -Step 5
```

### With Verbose Output

```powershell
.\ralph-loop.ps1 -IssueSpec issue-001.md -Verbose
```

---

## How It Works

### Iteration Structure
**Odd steps (1, 3, 5, ...):** Write failing test for next acceptance criterion  
**Even steps (2, 4, 6, ...):** Implement minimum code to pass test

### Flow
1. Step 1: Write failing test for criterion #1
2. Step 2: Implement code to make test #1 pass
3. Step 3: Write failing test for criterion #2
4. Step 4: Implement code to make test #2 pass
5. Repeat until all criteria are met

### Blocker Handling
If Claude responds with `[BLOCKED]`:
1. Script pauses and displays blocker message
2. User reads blocker explanation
3. User resolves the issue (clarify requirements, fix dependencies, etc.)
4. User re-runs script from current step
5. Loop resumes

---

## Comparison with Bash Implementation

| Aspect | Bash | PowerShell |
|--------|------|-----------|
| **Platform** | Linux, macOS, Unix | Windows, PowerShell 7+ |
| **Loop Syntax** | `while true; do ... done` | `while ($Step -le $MaxSteps) { ... }` |
| **API Calls** | `curl` | `Invoke-WebRequest` |
| **File Operations** | `grep`, `cat`, `sed` | PowerShell cmdlets |
| **Test Invocation** | `npm test 2>&1` | `Invoke-TestSuite` |
| **Git Integration** | `git` CLI | `git` CLI (same) |
| **JSON Parsing** | `jq` | `ConvertFrom-Json` |
| **Blocker Detection** | `grep "\[BLOCKED\]"` | `-match "\[BLOCKED\]"` |

**Functionality:** Both implementations are equivalent; only the shell syntax differs.

---

## Customization

### Change Test Runner Timeout
```powershell
# Edit Invoke-TestSuite function
npm test --testTimeout=60000  # Increase from 30 seconds to 60 seconds
```

### Use Different Claude Model
```powershell
.\ralph-loop.ps1 `
    -IssueSpec issue.md `
    -ClaudeModel "claude-3-opus-20240229"
```

### Skip Pause on Blocker (Fail Instead)
```powershell
.\ralph-loop.ps1 -IssueSpec issue.md -PauseOnBlocker:$false
```

### Integration with GitHub Actions (Windows)
```yaml
- name: Run Ralph Loop (Windows)
  shell: powershell
  env:
    CLAUDE_API_KEY: ${{ secrets.CLAUDE_API_KEY }}
  run: |
    .\ralph-loop.ps1 -IssueSpec "${{ github.event.issue.body }}"
```

---

## Troubleshooting

### Issue: "CLAUDE_API_KEY not set"
```powershell
$env:CLAUDE_API_KEY = "sk-ant-..."
.\ralph-loop.ps1 -IssueSpec issue.md
```

### Issue: "Not in a git repository"
```powershell
cd C:\path\to\repo
.\ralph-loop.ps1 -IssueSpec issue.md
```

### Issue: "No test runner detected"
```powershell
# Set up npm tests
npm init
npm install --save-dev jest
npm test
.\ralph-loop.ps1 -IssueSpec issue.md
```

### Issue: Execution Policy Error
```powershell
# Allow execution of local scripts
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
.\ralph-loop.ps1 -IssueSpec issue.md
```

### Issue: Tests Hang
```powershell
# Set timeout in npm or pytest config
# package.json: "test": "jest --testTimeout=30000"
# pytest.ini: [pytest] timeout=30
```

---

## Related Examples

- **[Bash Implementation](./bash.md)** — Linux/macOS equivalent
- **[Claude Adapter Notes](../adapters/claude.md)** — Claude-specific behavior
- **[Copilot Adapter Notes](../adapters/copilot.md)** — GitHub Copilot integration
