# Playbook: Configure Claude Code Hooks

## Purpose

Use Claude Code hooks to add lightweight guardrails around an agent session without baking team-specific rules into canonical patterns. Hooks are best for reminders, routing suggestions, post-edit checks, session continuity, and pre-commit safety checks.

This is a Claude Code-specific playbook. For the harness-agnostic workspace structure that should hold shared standards and scripts, see [Unified Agent Workspace](../patterns/unified-agent-workspace.md).

For copied hook recipes organized by lifecycle stage, see the [Claude Code Hooks Adapter](../adapters/claude-code-hooks/README.md).

Use Claude's official documentation for exact hook schema, supported events, matcher syntax, and lifecycle behavior:

- [Claude Code hooks reference](https://code.claude.com/docs/en/hooks)
- [Claude Code hooks guide](https://code.claude.com/docs/en/hooks-guide)

## When to Use

- You want Claude Code to surface team standards at the moment they matter.
- You need pre-flight routing suggestions before implementation starts.
- You want post-edit checks for formatting, type errors, debug logging, architecture, or domain rules.
- You want pre-commit checks for secrets, coverage, or architectural violations.
- You want session context restored between Claude Code sessions.

## When Not to Use

- The rule must be enforced for all contributors regardless of agent harness. Put it in CI, pre-commit, or repository policy instead.
- The hook would run slow global analysis after every small edit.
- The check needs nuanced human judgment and would produce noisy warnings.
- The behavior is a canonical engineering pattern. Document the pattern once, then use hooks only as a Claude Code adapter.

## Hook Categories

| Category | Claude Code Event | Use For | Mode |
|---|---|---|---|
| Pre-prompt routing | `UserPromptSubmit` or equivalent prompt hook | Complexity scoring, agent or skill recommendations | Advisory |
| Pre-tool safety | `PreToolUse` | Reminders before risky commands such as `git push` | Advisory or blocking |
| Post-edit quality | `PostToolUse` | Type checks, formatters, console/debug checks, standards reminders | Usually advisory |
| Session continuity | `SessionStart`, `SessionEnd` | Restore branch and recent-file context | Advisory |
| Commit safety | Git pre-commit invoked from Claude workflow | Secrets, coverage, architecture checks | Blocking when reliable |

Prefer advisory hooks until false positives are understood. Make a hook blocking only when it detects a high-confidence issue, such as staged secrets.

## Setup Shape

Keep team-owned hooks in a shared control-plane directory rather than scattering copies across every service repository:

```text
team-claude-config/
  hooks/
    pre-user-prompt/
    postcode/
    post-commit/
    optional-hooks/
  skills/
  scripts/
```

Then reference the shared directory from Claude Code settings:

```json
{
  "skillDirectories": [
    "~/team-claude-config/skills",
    "~/.claude/skills"
  ],
  "hooksDirectory": "~/team-claude-config/hooks",
  "env": {
    "TEAM_CLAUDE_CONFIG": "~/team-claude-config"
  }
}
```

Add hook entries selectively. Do not enable every hook by default.

## Recommended Hook Set

| Hook | Trigger | Purpose | Performance | Default |
|---|---|---|---|---|
| Task complexity check | Before processing implementation prompts | Suggest a stronger workflow for complex tasks | Low | Recommended |
| Agent router | Before processing prompts | Suggest specialized agents or skills based on task signals | Low to medium | Optional |
| TypeScript checker | After editing `.ts` or `.tsx` files | Run `tsc --noEmit` and report relevant errors | Medium | Recommended for TypeScript repos |
| Prettier formatter | After editing JS/TS files | Format the edited file | Low | Team preference |
| Console detector | After editing JS/TS files | Warn on `console.log` | Low | Recommended |
| Session persistence | Session start/end | Restore branch and recently edited files | Low | Recommended |
| Git push reminder | Before `git push` | Prompt review before publishing | Very low | Optional |
| Secrets detection | Before commit | Block staged secrets or sensitive files | Low | Recommended as blocking |
| Coverage check | Before commit | Warn or block when changed Java source lacks test coverage | Medium | Team policy |
| Architecture check | Before commit or post-edit | Detect layer and dependency rule violations | Medium | Advisory first |

## Example: Post-Edit Type Check

Use a `PostToolUse` hook scoped to edited TypeScript files:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "tool == \"Edit\" && tool_input.file_path matches \"\\\\.(ts|tsx)$\"",
        "hooks": [
          {
            "type": "command",
            "command": "bash \"${TEAM_CLAUDE_CONFIG}/optional-hooks/typescript/check-types.sh\""
          }
        ],
        "description": "TypeScript type check after editing .ts/.tsx files"
      }
    ]
  }
}
```

The hook script should read Claude's JSON input from stdin, extract the edited file path, find the nearest `tsconfig.json`, run `npx tsc --noEmit --pretty false`, and print only errors relevant to the edited file. Keep it non-blocking unless the team explicitly wants edits rejected.

## Example: Pre-Prompt Complexity Check

Use a prompt hook to detect implementation requests that may need a stronger loop:

```bash
COMPLEX_KEYWORDS=(
  "refactor"
  "redesign"
  "migration"
  "implement.*authentication"
  "distributed transaction"
  "database.*migration"
)

MODERATE_KEYWORDS=(
  "add.*endpoint"
  "new.*api"
  "change.*schema"
  "add.*validation"
)
```

Score the prompt, then print an advisory recommendation such as:

```text
HIGH COMPLEXITY TASK DETECTED
Recommendation: use an auto-review loop or Goal Loop with explicit verification evidence.
```

Do not auto-route every complex prompt. Let the assistant or human decide whether to invoke a stronger workflow.

## Example: Post-Edit Standards Reminder

Use post-edit hooks for checks that are useful but too contextual for hard blocking:

- SOLID reminders for Java service or use-case changes.
- Domain-driven design reminders when entities, aggregates, value objects, repositories, or services change.
- API standards checks when REST controller annotations appear in the diff.
- Architecture checks that detect domain-to-infrastructure imports or large classes.

Keep these hooks concise. A good standards hook reports the triggering evidence, the checklist, and the next action, such as running a dedicated architecture or clean-code skill.

## Example: Secrets Detection

Secrets detection is a good candidate for blocking because staged secrets are high-impact and usually machine-detectable:

```bash
STAGED_DIFF=$(git diff --cached --diff-filter=AM 2>/dev/null)

if echo "$STAGED_DIFF" | grep -nE '^\+.*(AKIA[0-9A-Z]{16}|aws_secret_access_key\s*[=:])'; then
  echo "BLOCKED: AWS credentials detected in staged changes" >&2
  exit 1
fi
```

Include checks for hardcoded passwords, API keys, private keys, connection strings with embedded credentials, and sensitive file names such as `.env`, `.pem`, `.key`, `.p12`, `.jks`, or keystore files.

## Operating Rules

1. Start hooks in advisory mode.
2. Log hook failures separately from hook findings.
3. Scope expensive hooks by file extension, path, or tool name.
4. Print short, actionable output; avoid dumping full compiler or diff output.
5. Make blocking hooks deterministic and explain exactly how to fix or bypass false positives.
6. Keep shared hooks in a control-plane repository when they apply across multiple services.
7. Mirror any mandatory hook rule in CI so non-Claude contributors get the same enforcement.

## Validation Checklist

- [ ] The hook has a narrow matcher.
- [ ] The hook exits `0` for irrelevant files or missing optional tooling.
- [ ] Advisory hooks do not block implementation.
- [ ] Blocking hooks detect high-confidence issues only.
- [ ] Output is short enough to be useful inside an agent transcript.
- [ ] Team-specific standards are linked to their canonical docs or skills.
- [ ] Mandatory rules also run outside Claude Code.

## Related

- [Claude Code Hooks Adapter](../adapters/claude-code-hooks/README.md) - Organized hook example library.
- [Unified Agent Workspace](../patterns/unified-agent-workspace.md) - Where shared hooks, skills, standards, and scripts belong in a multi-repo agent workspace.
- [Goal Loop](../patterns/goal-loop/README.md) - Hooks can provide stronger control boundaries when custom evaluators must govern continuation.
- [TDD Goal Loop Claude Adapter](../patterns/tdd-goal-loop/adapters/claude.md) - Claude Code subagent orchestration with optional hook/controller authority.
- [Repository Exploration](repository-exploration.md) - Use hooks and shared instructions only after understanding repository conventions.
- [Claude Code hooks reference](https://code.claude.com/docs/en/hooks) - Official hook schema, events, and behavior.
- [Claude Code hooks guide](https://code.claude.com/docs/en/hooks-guide) - Official practical guide for using hooks.

## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Claude Code hook recipes | User-provided hook examples | Practitioner examples | Provided optional productivity, pre-prompt routing, post-edit quality, and pre-commit safety hook examples for handbook integration | N/A |
| Claude Code hooks | Claude Code documentation | Documentation | Hook events, matchers, command hooks, settings, and lifecycle behavior | https://code.claude.com/docs/en/hooks |
| Claude Code hooks guide | Claude Code documentation | Documentation | Practical setup guidance and examples for using hooks safely | https://code.claude.com/docs/en/hooks-guide |
| Unified control-plane placement | Unified Agent Workspace pattern | Handbook pattern | Shared hooks belong with cross-repository agent standards when they apply across services | ../patterns/unified-agent-workspace.md |
