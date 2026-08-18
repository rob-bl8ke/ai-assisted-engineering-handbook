# Playbook: Repository Exploration

## Overview

This playbook provides a systematic approach to understanding an unfamiliar repository quickly. Whether you're exploring a codebase for the first time or bringing an AI agent into a new repository, this playbook helps you gather essential context efficiently.

## When to Use This Playbook

- You're new to a repository and need to understand its structure
- You're onboarding an AI agent to work in an unfamiliar codebase
- You need to evaluate a repository before beginning implementation work
- You're investigating a repository to understand dependencies and integrations
- You're preparing a fresh-context review (see [Software Development Process](../processes/software-development.md#phase-5-validation))

## Key Principle: Context Management

This playbook applies the [Context Management Pattern](../patterns/context-management.md) to systematically gather and organize information. Avoid both context overload (gathering everything) and context underload (missing critical information).

For large repositories, consider using [Repository Mental Model Tools](../references/repository-mental-model-tools.md) to precompute or query architecture, dependencies, symbol relationships, and cross-artifact links before manually exploring files. These tools do not replace exploration judgement; they reduce repeated rediscovery.

---

## Phase 1: High-Level Architecture (15-30 minutes)

### 1.1 Identify Repository Purpose

**Objective:** Understand what this repository does at a high level.

**Steps:**
1. Read the `README.md` at the repository root
2. Check `CONTEXT.md` (if it exists) for architectural context
3. Review the root-level `docs/` directory structure
4. Look for a `PRD.md`, `VISION.md`, or similar specification
5. Identify the primary technology stack

**What to capture:**
- One-sentence project purpose
- Primary technology stack (language, framework, platform)
- Key dependencies
- Target users or use cases

**Human execution:** Read files in your text editor or GitHub UI
**AI agent execution:** Ask the agent to summarize the README and identify the core purpose

**Example output:**
> "This is a Node.js React application for managing AI-assisted code reviews. Uses TypeScript, ESLint, Jest, and Docker."

### 1.2 Map Directory Structure

**Objective:** Understand how the codebase is organized.

**Steps:**
1. Examine the root-level directory layout
2. Identify primary source directories (e.g., `src/`, `lib/`, `app/`)
3. Locate configuration files (`package.json`, `.env.example`, `tsconfig.json`, `.eslintrc`, etc.)
4. Find test directories (`tests/`, `__tests__/`, `test/`)
5. Identify documentation directories (`docs/`, `docs/api/`, etc.)

**What to capture:**
- Source code directory names and purposes
- Configuration file names and purposes
- Test directory structure
- Documentation organization

**Directory exploration checklist:**
```
[ ] Root-level directories identified
[ ] Main source directory located
[ ] Test directory structure understood
[ ] Configuration files catalogued
[ ] Documentation directory layout known
[ ] Build/deployment directories identified (if any)
```

**Human execution:** Use file explorer or terminal: `ls -la` (Unix) / `dir` (PowerShell)
**AI agent execution:** Ask the agent to list directory structure and explain each folder's purpose

**Example structure:**
```
project-root/
├── src/                    # Application source code
├── tests/                  # Test files
├── docs/                   # Documentation
├── .github/                # GitHub-specific config
├── config/                 # Configuration templates
├── scripts/                # Build/utility scripts
├── package.json            # Dependencies and metadata
├── tsconfig.json           # TypeScript configuration
└── README.md               # Main documentation
```

---

## Phase 2: Code Organization (20-40 minutes)

### 2.1 Understand Module Boundaries

**Objective:** Identify major modules and their responsibilities.

**Steps:**
1. In the main source directory, identify top-level subdirectories or files
2. For each subdirectory, read its `README.md` or `index.ts/js` to understand its purpose
3. Identify which modules are:
   - Core/essential (must-understand)
   - Utilities/helpers (lower priority)
   - Infrastructure/setup (understand later)
   - Examples/tests (reference only)

**What to capture:**
- Module names and their primary responsibility
- Which modules are interdependent
- Which modules are entry points

**Module discovery checklist:**
```
[ ] Top-level modules identified
[ ] Core vs. utility modules distinguished
[ ] Module responsibilities documented
[ ] Dependencies between modules noted
[ ] Entry points identified
```

**Human execution:** Browse source files in your editor; look for comments or docstrings
**AI agent execution:** Ask the agent to provide a module map with explanations

**Example module map:**
```
src/
├── core/              → Core business logic (MUST UNDERSTAND)
│   ├── api.ts         → API integration
│   ├── processor.ts   → Main processing engine
│   └── types.ts       → Shared type definitions
├── utils/             → Utility functions (REFERENCE)
│   ├── logging.ts
│   └── validators.ts
└── adapters/          → External integrations (UNDERSTAND LATER)
    ├── database.ts
    └── queue.ts
```

### 2.2 Identify Critical Patterns and Conventions

**Objective:** Understand code style and architectural patterns used.

**Steps:**
1. Review coding conventions document (if it exists)
2. Examine a few key files in the core module
3. Identify architectural patterns (MVC, layered, etc.)
4. Note naming conventions for files, functions, classes, variables
5. Identify error handling patterns
6. Check for linting/formatting rules (`.eslintrc`, `prettier.config.js`)

**What to capture:**
- Naming conventions (camelCase, snake_case, PascalCase, kebab-case where each is used)
- Architectural pattern (if any)
- File organization patterns
- Error handling approach
- Common abstractions (middleware, decorators, etc.)

**Patterns checklist:**
```
[ ] File naming convention identified
[ ] Function/method naming convention identified
[ ] Class naming convention identified
[ ] Architecture pattern identified
[ ] Error handling pattern identified
[ ] Common imports/utilities identified
```

**Human execution:** Read several source files; check configuration files
**AI agent execution:** Ask the agent to analyze a few key files and summarize conventions

**Example conventions summary:**
> "Files are named in kebab-case (user-manager.ts). Exports use PascalCase for classes, camelCase for functions. Error handling uses try-catch with custom AppError class. Architecture follows layered pattern: models → services → controllers → routes."

---

## Phase 3: Build and Test Infrastructure (15-25 minutes)

### 3.1 Understand Build and Test Setup

**Objective:** Know how to build, test, and run the code.

**Steps:**
1. Read `package.json` / build configuration file
2. Identify build commands and what they do
3. Identify test framework and test structure
4. Check for pre-commit hooks or CI/CD configuration
5. Identify any environment setup requirements

**What to capture:**
- How to build/compile the project
- How to run tests (unit, integration, end-to-end)
- How to run the application locally
- What environment variables are needed
- CI/CD pipeline stages (if any)

**Build checklist:**
```
[ ] Build command known
[ ] Test command known
[ ] Start/run command known
[ ] Required environment variables documented
[ ] Test coverage tool identified
[ ] CI/CD pipeline understood
```

**Human execution:** 
```bash
# Unix/Bash
cat package.json | grep -A5 '"scripts"'

# PowerShell
Get-Content package.json | Select-String -A5 '"scripts"'
```

**AI agent execution:** Ask the agent to explain build commands and test setup

**Example commands:**
```json
{
  "scripts": {
    "build": "tsc && webpack",
    "test": "jest --coverage",
    "start": "node dist/index.js",
    "dev": "ts-node src/index.ts"
  }
}
```

### 3.2 Verify Local Setup

**Objective:** Ensure you can build and test locally.

**Steps:**
1. Check for setup scripts or documentation (`.github/CONTRIBUTING.md`)
2. Verify Node version requirements or language prerequisites
3. Install dependencies
4. Run build command
5. Run test suite
6. Verify tests pass

**Setup verification checklist:**
```
[ ] Prerequisites verified (Node version, etc.)
[ ] Dependencies installed
[ ] Build succeeds
[ ] All tests pass
[ ] Local run/start verified
```

**Common issues and solutions:**
- Tests fail: Check for missing environment variables in `.env.example`
- Build fails: Verify TypeScript configuration and all dependencies installed
- Missing dependencies: Run `npm install` or `yarn install`
- Node version mismatch: Check `.nvmrc` or `engines` in `package.json`

**Human execution:** Follow documentation or README's Getting Started section
**AI agent execution:** Ask the agent to walk through setup and report any issues

---

## Phase 4: Critical Paths and Flows (20-30 minutes)

### 4.1 Trace a Key User Flow

**Objective:** Understand how the system works end-to-end.

**Steps:**
1. Identify the most common or important user action/flow
2. Trace the code from entry point (e.g., API endpoint, CLI command, UI interaction) through the system
3. Note key functions, data transformations, and module interactions
4. Identify external dependencies or integrations involved
5. Document the flow visually or textually

**What to capture:**
- Flow name and purpose
- Entry point (API, function, command)
- Major steps in the flow
- Key data structures involved
- External integrations (database, APIs, etc.)
- Error cases or edge conditions

**Flow trace checklist:**
```
[ ] Entry point identified
[ ] Happy path traced
[ ] Key functions documented
[ ] Data transformations noted
[ ] Error paths identified
[ ] Integration points noted
```

**Human execution:** Use your editor's Go to Definition feature; trace function calls
**AI agent execution:** Ask the agent to trace a specific user flow and document it

**Example flow:**
```
User Request (GET /api/code-review/:id)
  ↓
routes/review.ts (endpoint handler)
  ↓
services/reviewService.ts (business logic)
  ↓
adapters/database.ts (fetch review from DB)
  ↓
models/Review.ts (data validation)
  ↓
Response (JSON)
```

### 4.2 Understand Data Models

**Objective:** Know what data the system manages and how it's structured.

**Steps:**
1. Locate data model definitions (interfaces, types, schemas, database models)
2. For each major data type, identify:
   - Required fields
   - Optional fields
   - Relationships to other models
   - Persistence strategy (database, cache, memory, etc.)
3. Document the data flow through the system

**What to capture:**
- Primary data models/entities
- Key relationships (one-to-many, many-to-many, etc.)
- Where data is persisted
- Any data validation rules

**Models checklist:**
```
[ ] Primary models identified
[ ] Model relationships documented
[ ] Persistence layer identified
[ ] Validation rules noted
[ ] Key relationships to other systems noted
```

**Human execution:** Search for type definitions or database schema files
**AI agent execution:** Ask the agent to map out the data models and their relationships

---

## Phase 5: Dependencies and Integrations (15-20 minutes)

### 5.1 Catalog External Dependencies

**Objective:** Understand what external systems this codebase depends on.

**Steps:**
1. Review package.json/requirements files for third-party libraries
2. Identify which dependencies are core vs. optional
3. Check for environment-specific dependencies
4. Note any cloud service dependencies (AWS, Azure, etc.)
5. Identify any database requirements
6. Note any API integrations to external services

**What to capture:**
- Major third-party libraries and their purpose
- Database technology and version requirements
- External APIs or services needed
- Authentication/authorization systems used
- Message queues or event systems (if any)

**Dependencies checklist:**
```
[ ] Major libraries understood
[ ] Database technology identified
[ ] API integrations documented
[ ] Authentication mechanism understood
[ ] Third-party services catalogued
[ ] Version constraints noted
```

**Human execution:** Review `package.json` and configuration files
**AI agent execution:** Ask the agent to list key dependencies and explain their purpose

**Example dependencies:**
```
Core:
- express → HTTP server framework
- typescript → Type system
- postgresql → Primary database

External Services:
- AWS S3 → File storage
- SendGrid → Email delivery
```

### 5.2 Understand Authentication and Authorization

**Objective:** Know how the system handles user authentication and permissions.

**Steps:**
1. Identify the authentication mechanism (JWT, OAuth, sessions, etc.)
2. Locate where authentication is verified
3. Identify authorization patterns (role-based, permission-based, etc.)
4. Find middleware or decorators handling auth checks
5. Note any special cases or exemptions

**What to capture:**
- Authentication method
- Authorization pattern
- Where auth checks happen
- Special cases (public endpoints, admin overrides, etc.)

**Auth checklist:**
```
[ ] Authentication method identified
[ ] Authorization pattern identified
[ ] Auth middleware/decorators located
[ ] Public vs. protected endpoints distinguished
[ ] Special cases documented
```

---

## Phase 6: Recent Activity and Known Issues (10-15 minutes)

### 6.1 Review Recent Changes

**Objective:** Understand what's been worked on recently and any known issues.

**Steps:**
1. Check recent git commits (last 20-30)
2. Review open issues and pull requests
3. Look for any TODO/FIXME comments in code
4. Check the CHANGELOG or release notes
5. Review any project board or issue tracker

**What to capture:**
- Recent features or bug fixes
- Known issues or limitations
- Planned improvements
- Active problem areas

**Recent activity checklist:**
```
[ ] Recent commits reviewed
[ ] Open issues noted
[ ] Known limitations documented
[ ] TODO comments catalogued
[ ] Problem areas identified
```

**Human execution:** 
```bash
# Unix/Bash
git log --oneline -20

# PowerShell
git log --oneline -20
```

**AI agent execution:** Ask the agent to summarize recent changes and known issues

### 6.2 Identify Quality and Test Coverage

**Objective:** Understand code quality standards and test coverage.

**Steps:**
1. Run the test suite (if possible)
2. Check test coverage reports
3. Review code quality tools (linters, formatters)
4. Identify any code smells or technical debt
5. Note areas with low test coverage

**What to capture:**
- Overall test coverage percentage
- Areas with good test coverage
- Areas with poor/missing tests
- Code quality standards used
- Any quality improvement efforts in progress

**Quality checklist:**
```
[ ] Test coverage measured
[ ] High-risk areas identified (low coverage)
[ ] Code quality tools identified
[ ] Linting rules understood
[ ] Technical debt noted
```

**Human execution:** Run test suite with coverage flag
**AI agent execution:** Ask the agent to analyze test coverage and identify gaps

---

## Phase 7: Consolidate and Plan (10 minutes)

### 7.1 Create a Repository Summary

**Objective:** Document everything you've learned in a reusable format.

**Steps:**
1. Create or update a `REPOSITORY_CONTEXT.md` file in `docs/` with:
   - High-level purpose and technology stack
   - Architecture overview (directory structure, modules)
   - Key data models
   - How to build, test, and run
   - Critical user flows
   - External dependencies and integrations
   - Known issues and technical debt
   - How to contribute

2. Keep this file updated as the codebase evolves
3. Use this as context for onboarding new team members and AI agents

**Example structure:**
```markdown
# Repository Context

## Quick Facts
- Purpose: Code review automation system
- Language: TypeScript
- Framework: Express + React
- Database: PostgreSQL

## How to Get Started
1. Install Node 18+
2. Run `npm install`
3. Configure `.env` from `.env.example`
4. Run `npm test` to verify setup

## Architecture
- src/core/ - Business logic
- src/adapters/ - External integrations
- src/utils/ - Helper functions

## Key Flows
- User creates review → stored in DB → sent to AI → results returned
- ...
```

### 7.2 Decide What to Explore Next

**Objective:** Identify what you need to understand before starting implementation work.

**Based on your assignment or goals:**
- [ ] Need to modify a specific module? Dive deep into that module's code
- [ ] Need to add a feature? Understand the relevant data models and flows
- [ ] Need to fix a bug? Review the bug report and trace related code paths
- [ ] Starting fresh work? Continue to [Decompose Work Playbook](decompose-work.md)

---

## For AI Agents

### What This Playbook Tells You

Follow these phases to understand a repository before implementation:

1. **Architecture First:** Learn the high-level structure before reading code
2. **Build & Test:** Verify you can run and test locally
3. **Trace Flows:** Understand how users' requests flow through the system
4. **Know Dependencies:** Understand what external systems matter
5. **Consolidate Knowledge:** Document what you've learned

### How to Execute Efficiently

- Create summaries at each phase before moving to the next
- If something is unclear, add it to a "Questions" list rather than diving deep
- Stop exploration when you have enough context to start implementation
- Use the [Context Management Pattern](../patterns/context-management.md) to avoid context overload

### Integration with Software Development Process

This playbook supports **Phase 1 (Discovery)** of the [Software Development Process](../processes/software-development.md), specifically the "Shared Understanding" step where teams ensure everyone understands the problem domain and existing system.

---

## Common Pitfalls

| Pitfall | What Goes Wrong | Solution |
|---------|-----------------|----------|
| **Analysis Paralysis** | Trying to understand everything before starting | Set a time box (30-60 min). Explore deeply only what you need for your current task. |
| **Context Overload** | Gathering too much information | Focus on: architecture, build/test, and one key flow. Skip deep dives into non-critical areas. |
| **Ignoring Tests** | Skipping test exploration | Tests reveal intended usage and edge cases. Always examine test structure. |
| **Missing Local Setup** | Assuming build works without verifying | Always run build and tests locally. Document issues you encounter. |
| **Forgetting Recent Context** | Not checking recent changes or issues | Recent PRs and issues often reveal the current state and pain points. |

---

## Related Patterns and Playbooks

- **Pattern:** [Context Management](../patterns/context-management.md) — Deliberately control information provided to avoid overload
- **Process:** [Software Development Process - Phase 1 (Discovery)](../processes/software-development.md#phase-1-discovery) — Where repository exploration fits in the larger workflow
- **Playbook:** [Decompose Work](decompose-work.md) — Next step after understanding a repository

---

## References

- [Repository Exploration Scenario](https://github.com/rob-bl8ke/ai-assisted-engineering-handbook/PRD.md#scenario-i-have-inherited-an-unfamiliar-repository) — User scenario in the handbook
- [Context Management Pattern](../patterns/context-management.md) — Detailed pattern on managing information flow
- [Software Development Process](../processes/software-development.md) — Full process this playbook supports
