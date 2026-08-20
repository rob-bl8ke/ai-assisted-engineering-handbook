# Agent Workspace Lab

## Purpose

Use this lab to prove that repository-owned skills, custom agents, personal/WIP capabilities, external dependencies, and CI/cloud execution can work together without depending on a developer's user profile.

The lab operationalizes these patterns:

- [Scheduled Agentic Work](../patterns/scheduled-agentic-work.md) for runtime portability, bootstrap, preflight validation, and scheduled execution.
- [Unified Agent Workspace](../patterns/unified-agent-workspace.md) for shared agent-facing repository structure.
- [Skills](../skills/README.md) for deciding which capabilities are committed repository policy versus external dependencies.

The central proof is:

```text
fresh clone
  -> repository instructions only
  -> bootstrap managed capabilities
  -> verify expected skills and agents
  -> run the same review locally and in CI
```

## Working Rules

- Repository-owned capabilities are committed.
- Personal or WIP capabilities remain local and invisible to Git.
- External dependencies must be reproducibly restorable.
- CI may depend only on committed or reproducibly restorable capabilities.
- Do not automate a compatibility problem until you have manually observed it once.

Use the rule from [Scheduled Agentic Work](../patterns/scheduled-agentic-work.md#runtime-portability): commit intent; lock dependencies.

## Lab 0: Create The Repository

Create a neutral lab repository:

```bash
mkdir agent-workspace-lab
cd agent-workspace-lab

git init
mkdir -p lab/sample-service
mkdir -p .agents/skills
mkdir -p .claude/agents
mkdir -p .github/agents
mkdir -p .opencode/agents
mkdir -p tools
mkdir -p docs
```

Start with this structure:

```text
agent-workspace-lab/
+-- README.md
+-- AGENTS.md
+-- .gitignore
+-- .agents/
|   +-- skills/
+-- .claude/
|   +-- agents/
+-- .github/
|   +-- agents/
+-- .opencode/
|   +-- agents/
+-- lab/
|   +-- expected-findings.md
|   +-- sample-service/
+-- docs/
+-- tools/
```

Keep `.gitignore` small at first:

```gitignore
# Local/private experiments
.local-agents/

# Tool/runtime noise
node_modules/
.DS_Store
```

Do not ignore `.agents`, `.claude`, `.github`, or `.opencode` yet. Observe what each tool creates before deciding what should be committed, ignored, or reconstructed.

Commit the baseline:

```bash
git add .
git commit -m "Initialize agent workspace lab"
```

## Lab 1: Establish A Deterministic Target

Create `lab/sample-service/order-service.js`:

```javascript
async function submitOrder(order) {
  while (true) {
    try {
      const response = await fetch(
        "https://payments.example.com/pay",
        {
          method: "POST",
          body: JSON.stringify(order)
        }
      );

      return await response.json();
    } catch (error) {
      // Try again
    }
  }
}

async function saveOrder(db, order) {
  try {
    await db.insert(order);
  } catch (error) {
    // Ignore persistence errors
  }
}
```

Create `lab/expected-findings.md` as the oracle:

```markdown
# Expected Findings

1. HTTP request has no explicit timeout.
2. Retry is unbounded.
3. Retry has no backoff or jitter.
4. Persistence failure is swallowed.
5. Retrying a POST payment operation may duplicate the side effect if the operation is not idempotent.
```

The sample is intentionally small. The point is to measure whether agent/skill packaging changes capability, not whether the service is realistic.

## Lab 2: Baseline `npx skills`

Inspect an existing skill repository before installing:

```bash
npx skills add vercel-labs/agent-skills --list
```

Install project-scoped skills for selected harnesses. Do not use `-g` because user scope is not portable to CI:

```bash
npx skills add vercel-labs/agent-skills \
  --skill skill-creator \
  -a claude-code \
  -a opencode
```

Inspect the result:

```bash
git status
find . -maxdepth 4 -type f | sort
```

Record observations in `docs/npx-skills-observations.md`:

```text
Where is the canonical copy?
Did it create symlinks?
What appeared under .claude?
What appeared under .opencode?
What would be committed?
What disappears after a fresh clone?
```

Labs 0-2 are the first slice. Stop here before introducing more tools so the baseline remains clean.

## Lab 3: Prove Cross-Harness Installation

Repeat installation for Copilot after using the CLI's current supported-agent list or interactive mode to discover the exact target name:

```bash
npx skills add <skill-repository> \
  --skill <skill-name> \
  -a claude-code \
  -a opencode \
  -a <copilot-target>
```

The conceptual result to verify is:

```text
canonical skill
      ->
Claude / OpenCode / Copilot adapters
```

This stage tests installation and wiring, not skill quality.

## Lab 4: Create A Repository-Owned Skill

Create `.agents/skills/resilience-review/SKILL.md`:

```markdown
---
name: resilience-review
description: Review software for runtime and operational resilience weaknesses.
---

# Resilience Review

When reviewing code, look specifically for:

- missing timeouts
- unlimited retries
- retries without backoff or jitter
- non-idempotent retried operations
- swallowed failures
- resource exhaustion
- missing failure isolation

Prioritize concrete failure scenarios over style observations.

For each finding provide:

1. evidence
2. failure scenario
3. likely impact
4. suggested mitigation

Do not modify code unless explicitly requested.
```

Commit it:

```bash
git add .agents/skills/resilience-review
git commit -m "Add repository resilience review skill"
```

Record which harnesses consume `.agents/skills` directly and which need a native adapter or symlink.

## Lab 5: Create The First Agent Manually

Create the canonical role source at `agents/resilience-reviewer.md`:

```bash
mkdir agents
```

```markdown
---
name: resilience-reviewer
description: Reviews a repository for actionable runtime and operational resilience flaws.
---

You are a specialist resilience reviewer.

Use the `resilience-review` skill.

Review evidence before making a finding.

Focus on failure modes that could affect runtime behaviour, availability, correctness, recoverability, or operational safety.

Do not modify source code.

If the required `resilience-review` skill is unavailable, say so rather than silently performing a generic review.
```

Manually create minimal native wrappers under:

```text
.claude/agents/resilience-reviewer.md
.github/agents/resilience-reviewer.agent.md
.opencode/agents/resilience-reviewer.md
```

Do not automate wrapper generation yet. First learn the native differences.

## Lab 6: First Behavioural Test

Ask each harness the same thing:

```text
Use the resilience-reviewer agent to review lab/sample-service/order-service.js.

Do not modify the source.

Report only actionable resilience findings.
```

Capture outputs under:

```text
lab/results/
+-- claude.md
+-- copilot.md
+-- opencode.md
```

Compare each result against `lab/expected-findings.md`:

| Expected finding | Claude | Copilot | OpenCode |
|---|---:|---:|---:|
| Missing timeout | | | |
| Unlimited retry | | | |
| No backoff | | | |
| Swallowed DB error | | | |
| Retried POST/idempotency | | | |

The question is not which model writes the nicest answer. The question is whether the same agent/skill concept survives across harnesses.

## Lab 7: Introduce Personal/WIP Configuration With `skul`

Only introduce `skul` after the baseline works:

```bash
npm install --global @solaqua/skul
```

Use it for personal, experimental, not-yet-team-policy capabilities. Its useful role in the lab is to materialize project-scoped skills and agents into native harness directories while hiding them from Git through `.git/info/exclude`.

Create or use an experimental bundle with:

```text
skills/
+-- resilience-review-v2/

agents/
+-- resilience-reviewer-v2/
```

After adding it with `skul`, verify:

```bash
git status
skul status
```

Expected result: Git remains clean while the harnesses can still see the private capability.

## Lab 8: Promotion Test

Prove this lifecycle:

```text
PRIVATE/WIP
  skul-managed resilience-review-v2
      ->
  improved and accepted
      ->
PROJECT CAPABILITY
  .agents/skills/resilience-review/
  agents/resilience-reviewer.md
      ->
  committed
```

Do not promote by merely removing an exclusion from a generated native file. Promote by making the accepted capability repository-owned source.

Then remove the private `skul` version and verify the repository still works.

## Lab 9: Introduce `dotagents`

Only introduce `dotagents` after manual wrappers and behavioural tests are understood:

```bash
npx @sentry/dotagents init
```

Use this stage to discover how `agents.toml`, `agents.lock`, `.agents/skills/`, subagent installation, project/user scope, and harness targets affect the repository.

After a configured clone, test:

```bash
npx @sentry/dotagents install
```

Measure what manual setup disappears and what remains necessary.

## Lab 10: Compare `npx skills` And `dotagents`

Make the overlap explicit:

| Tool | Hypothesis to test |
|---|---|
| `npx skills` | Good for finding and installing skills across harnesses. |
| `dotagents` | Better for committed project configuration, lock state, agents, MCP, hooks, and targets. |
| Both | `npx skills` may be exploration/discovery while `dotagents` becomes committed dependency management. |

Do not decide in advance. Let the lab evidence choose between keeping `npx skills`, replacing it with `dotagents`, or using both for different lifecycle stages.

## Lab 11: Fresh-Clone Proof

Clone the repository into a clean location:

```bash
git clone <repo-url> agent-workspace-lab-clean
cd agent-workspace-lab-clean
```

Do not copy user-level configuration from `~/.claude`, `~/.copilot`, `~/.config/opencode`, `~/.skul`, or similar locations.

Follow only repository instructions. The target flow is:

```bash
npx @sentry/dotagents install
./tools/verify-agent-environment.sh
```

The eventual output should be equivalent to:

```text
Repository capability check

Skills
  [x] resilience-review

Agents
  [x] resilience-reviewer

Harness adapters
  [x] Claude
  [x] OpenCode
  [x] Copilot

External dependencies
  [x] restored

Ready.
```

This proves that a clone can reconstruct trusted agent capability without depending on one developer's user profile.

## Lab 12: Clean-Machine Test

Repeat the fresh-clone proof in a container or VM.

This catches hidden assumptions such as global npm packages, personal symlinks, shell aliases, cached bundles, or existing user-level agents.

Treat this as the end of the portability proof of concept.

## Lab 13: CI

Only after the clean-machine test passes, add CI with manual dispatch only:

```text
workflow_dispatch
  ->
checkout
  ->
bootstrap
  ->
verify
  ->
agent review
  ->
Markdown artifact
```

Do not schedule it yet. Do not create issues. Do not write source.

## Lab 14: GitHub Agentic Workflows

Introduce GitHub Agentic Workflows after CI works.

Use `gh aw` to compare Markdown workflow definitions and workflow-declared skills against the local developer setup:

```text
developer environment
  -> npx skills / dotagents
  -> local agent

CI environment
  -> gh-aw skills
  -> workflow agent
```

The lab may intentionally use different installation machinery locally and in CI while preserving the same skill sources.

## Lab 15: First Scheduled Custodian

Only after manual CI works, add schedule:

```text
Every Monday
  ->
resilience-review workflow
  ->
resilience-reviewer
  ->
resilience-review skill
  ->
report only
```

Then graduate slowly:

```text
report
  ->
issue
  ->
deduplicated issue
  ->
incremental review
  ->
graph-assisted impact traversal
  ->
remediation agent
  ->
proposed PR
```

Follow the permission graduation guidance in [Scheduled Agentic Work](../patterns/scheduled-agentic-work.md#permission-graduation).

## Success Criteria

- Labs 0-2 produce a committed baseline and `npx skills` observation notes.
- The resilience reviewer finds the expected sample-service issues across the target harnesses.
- Personal/WIP capabilities can stay invisible to Git until promoted.
- Promoted capabilities become repository-owned source, not generated native artifacts.
- A fresh clone can bootstrap and verify required skills, agents, and adapters.
- A clean machine can reproduce the same environment without user-profile configuration.
- CI runs only from committed or reproducibly restorable capabilities.
- Scheduling is added only after manual CI succeeds.

## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Agent workspace lab sequence | User-provided lab design | Practitioner summary | Defines staged experiment for `npx skills`, `skul`, `dotagents`, fresh-clone proof, CI, `gh aw`, and scheduled custodians | N/A |
| `dotagents` | Sentry `dotagents` repository | Tool documentation | Documents `agents.toml`, `agents.lock`, managed skills/subagents, project/user scope, and install flow | https://github.com/getsentry/dotagents |
| `dotagents` configuration | Sentry `dotagents` repository | Tool documentation | Documents configuration and user scope behavior | https://github.com/getsentry/dotagents/blob/main/skills/dotagents/references/configuration.md |
| `npx skills` | `skills-cli` repository | Tool documentation | Documents project/global skill installation and multi-agent targets | https://github.com/antfu/skills-cli |
| `skul` | `skul` repository | Tool documentation | Documents project-scoped personal AI configuration bundles hidden through Git exclude mechanisms | https://github.com/sjquant/skul |
| GitHub Agentic Workflows | `gh-aw` documentation | Tool documentation | Documents Markdown workflow sources compiled into GitHub Actions workflows | https://github.com/github/gh-aw/blob/main/docs/src/content/docs/setup/creating-workflows.mdx |
| `gh-aw` skills | `gh-aw` repository | Tool documentation | Documents declaring external and local skills in workflow frontmatter | https://github.com/github/gh-aw/blob/main/.github/aw/skills.md |
