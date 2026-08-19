# Claude Code Hook Examples: Pre-Prompt Routing

## Purpose

Use pre-prompt hooks to inspect a user request before Claude Code starts work. These examples are advisory: they help the assistant choose an appropriate workflow, skill, or reviewer without blocking the prompt.

## Task Complexity Check

Use this when a prompt may need a stronger implementation loop, such as a Goal Loop, TDD Goal Loop, or auto-review workflow.

```bash
#!/bin/bash
# Pre-user-prompt hook: assess task complexity from the user request.

COMPLEX_KEYWORDS=(
  "refactor"
  "redesign"
  "rewrite"
  "migration"
  "breaking change"
  "new feature"
  "implement.*authentication"
  "implement.*authorization"
  "implement.*payment"
  "implement.*saga"
  "distributed transaction"
  "event sourcing"
  "new aggregate"
  "new bounded context"
  "new microservice"
  "kafka.*integration"
  "schema.*change"
  "database.*migration"
)

MODERATE_KEYWORDS=(
  "add.*endpoint"
  "new.*api"
  "update.*model"
  "change.*schema"
  "add.*validation"
  "implement.*feature"
)

check_prompt_complexity() {
  local prompt="$1"
  local score=0
  local prompt_lower
  prompt_lower=$(echo "$prompt" | tr '[:upper:]' '[:lower:]')

  for keyword in "${COMPLEX_KEYWORDS[@]}"; do
    if echo "$prompt_lower" | grep -qE "$keyword"; then
      score=$((score + 3))
      echo "Detected: $keyword (+3 complexity)" >&2
    fi
  done

  for keyword in "${MODERATE_KEYWORDS[@]}"; do
    if echo "$prompt_lower" | grep -qE "$keyword"; then
      score=$((score + 1))
      echo "Detected: $keyword (+1 complexity)" >&2
    fi
  done

  local and_count
  and_count=$(echo "$prompt_lower" | grep -o " and " | wc -l | tr -d ' ')
  if [ "$and_count" -ge 2 ]; then
    score=$((score + 2))
    echo "Multi-part task detected (+2 complexity)" >&2
  fi

  if echo "$prompt_lower" | grep -qE "(multiple files|several files|across.*services)"; then
    score=$((score + 2))
    echo "Multi-file or multi-service change (+2 complexity)" >&2
  fi

  echo "$score"
}

USER_PROMPT="${CLAUDE_USER_PROMPT:-}"
if [ -z "$USER_PROMPT" ] && [ ! -t 0 ]; then
  USER_PROMPT=$(cat)
fi

if [ -z "$USER_PROMPT" ]; then
  exit 0
fi

if ! echo "$USER_PROMPT" | grep -qiE "(implement|create|add|build|develop|write|refactor|change|update|fix)"; then
  exit 0
fi

complexity_score=$(check_prompt_complexity "$USER_PROMPT")
echo "Estimated complexity score: $complexity_score"

if [ "$complexity_score" -ge 5 ]; then
  echo "High complexity task detected. Consider a Goal Loop, TDD Goal Loop, or auto-review workflow."
elif [ "$complexity_score" -ge 3 ]; then
  echo "Moderate complexity task. Add explicit verification and review steps."
else
  echo "Standard task complexity."
fi

exit 0
```

## Agent Router

Use this when a workspace has named specialist agents or skills. Keep routing advisory; the assistant should still decide whether the recommendation fits the current task.

```bash
#!/bin/bash
# Pre-user-prompt hook: recommend specialist agents and skills.

set -euo pipefail

USER_PROMPT="${1:-}"
if [ -z "$USER_PROMPT" ]; then
  exit 0
fi

PROMPT_LOWER=$(echo "$USER_PROMPT" | tr '[:upper:]' '[:lower:]')

if echo "$PROMPT_LOWER" | grep -qE "^(what|where|how|why|explain|show|describe|list|tell me|can you)" && \
   ! echo "$PROMPT_LOWER" | grep -qE "(implement|create|add|build|write|fix|refactor|deploy)"; then
  exit 0
fi

if [ ${#USER_PROMPT} -lt 10 ]; then
  exit 0
fi

AGENTS=()
SKILLS=()

if echo "$PROMPT_LOWER" | grep -qE "(design|architect|plan|refactor|restructure|redesign|system design|scalability)"; then
  AGENTS+=("architect")
fi

if echo "$PROMPT_LOWER" | grep -qE "(complex|multi-step|large feature|major change|distributed|microservice)"; then
  AGENTS+=("planner")
fi

if echo "$PROMPT_LOWER" | grep -qE "(auth|authentication|authorization|login|password|token|jwt|oauth|security|payment|credit card|sensitive|encrypt|decrypt|api key|secret)"; then
  AGENTS+=("security-reviewer")
fi

if echo "$PROMPT_LOWER" | grep -qE "(database|sql|query|migration|schema|index|postgres|table|column|optimize query)"; then
  AGENTS+=("database-reviewer")
fi

if echo "$PROMPT_LOWER" | grep -qE "(test|tdd|unit test|integration test|coverage|e2e|end.to.end)"; then
  AGENTS+=("tdd-guide")
fi

if echo "$PROMPT_LOWER" | grep -qE "(aggregate|entity|value object|domain model|bounded context|ddd)"; then
  SKILLS+=("ddd-patterns")
fi

if echo "$PROMPT_LOWER" | grep -qE "(kafka|event.*driven|publish.*event|consume.*event|event.*sourcing)"; then
  SKILLS+=("event-driven-patterns")
fi

if echo "$PROMPT_LOWER" | grep -qE "(outbox|transactional.*outbox|event.*publishing|eventual.*consistency)"; then
  SKILLS+=("outbox-core-patterns")
fi

if [ ${#AGENTS[@]} -eq 0 ] && [ ${#SKILLS[@]} -eq 0 ]; then
  exit 0
fi

if [ ${#SKILLS[@]} -gt 0 ]; then
  echo "Recommended skills:"
  printf '  - %s\n' $(printf '%s\n' "${SKILLS[@]}" | sort -u)
fi

if [ ${#AGENTS[@]} -gt 0 ]; then
  echo "Recommended agents:"
  printf '  - %s\n' $(printf '%s\n' "${AGENTS[@]}" | sort -u)
fi

if [ ${#AGENTS[@]} -gt 1 ]; then
  echo "Orchestration: run planning or architecture first, then implementation, then specialist review."
fi

exit 0
```

## Design Notes

- Keep these hooks fast because they run before useful work starts.
- Avoid forcing a workflow based only on keywords.
- Prefer suggesting established handbook patterns such as [Goal Loop](../../patterns/goal-loop/README.md) or [TDD Goal Loop](../../patterns/tdd-goal-loop/README.md).
