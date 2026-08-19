# Claude Code Hooks Adapter

## Purpose

This adapter collects Claude Code hook examples for adding lightweight workflow guardrails around agent sessions. Use these examples with the [Configure Claude Code Hooks](../../playbooks/configure-claude-code-hooks.md) playbook.

Hooks are harness-specific. Keep canonical engineering rules in patterns, playbooks, standards, CI, or pre-commit tooling. Use Claude Code hooks to surface those rules at useful moments in the agent workflow.

For authoritative syntax, event names, matcher behavior, and lifecycle details, use Claude's official hook documentation:

- [Claude Code hooks reference](https://code.claude.com/docs/en/hooks)
- [Claude Code hooks guide](https://code.claude.com/docs/en/hooks-guide)

## Example Groups

| Group | File | Includes |
|---|---|---|
| Pre-prompt routing | [pre-prompt-routing.md](pre-prompt-routing.md) | Task complexity scoring, agent and skill routing |
| Post-edit quality | [post-edit-quality.md](post-edit-quality.md) | SOLID reminders, DDD checks, API standards checks |
| Commit safety | [commit-safety.md](commit-safety.md) | Test coverage, secrets detection, architecture checks |
| Optional productivity | [optional-productivity.md](optional-productivity.md) | TypeScript checks, Prettier, console detection, session persistence, git reminders |

## Placement

In a single repository, project-specific hooks can live under `.claude/hooks/` or another project-level Claude configuration directory.

In a multi-repository workspace, prefer the [Unified Agent Workspace](../../patterns/unified-agent-workspace.md) control-plane shape:

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

## Operating Modes

| Mode | Use For | Rule |
|---|---|---|
| Advisory | Complexity hints, routing suggestions, style reminders, architecture smells | Exit `0`; print concise guidance |
| Blocking | Secrets, unsafe commands, deterministic policy violations | Exit non-zero with a clear remediation |
| Formatting | Prettier or equivalent file-local formatters | Keep scoped to the edited file |
| Session | Restoring or saving context | Keep output short and avoid leaking secrets |

## Related

- [Configure Claude Code Hooks](../../playbooks/configure-claude-code-hooks.md)
- [Unified Agent Workspace](../../patterns/unified-agent-workspace.md)
- [Goal Loop Claude Adapter](../../patterns/goal-loop/adapters/claude.md)
- [TDD Goal Loop Claude Adapter](../../patterns/tdd-goal-loop/adapters/claude.md)
- [Claude Code hooks reference](https://code.claude.com/docs/en/hooks)
- [Claude Code hooks guide](https://code.claude.com/docs/en/hooks-guide)
