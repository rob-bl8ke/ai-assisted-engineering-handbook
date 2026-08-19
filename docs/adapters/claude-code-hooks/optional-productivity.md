# Claude Code Hook Examples: Optional Productivity

## Purpose

These optional hooks improve local feedback and session continuity. They should be individually enabled because their usefulness depends on stack, workflow, and performance tolerance.

## TypeScript Checker

Hook setup:

```json
{
  "name": "TypeScript Type Checker",
  "description": "Run tsc --noEmit after editing TypeScript files",
  "category": "code-quality",
  "impact": "medium",
  "performance": "~1-2s per edit",
  "recommended": true,
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

Script:

```bash
#!/bin/bash
set -e

INPUT_JSON=$(cat)
FILE_PATH=$(echo "$INPUT_JSON" | python3 -c '
import json, sys
try:
    data = json.loads(sys.stdin.read())
    print(data.get("tool_input", {}).get("file_path", ""))
except Exception:
    pass
')

if [ -z "$FILE_PATH" ] || [ ! -f "$FILE_PATH" ]; then
  echo "$INPUT_JSON"
  exit 0
fi

DIR=$(dirname "$FILE_PATH")
while [ "$DIR" != "/" ] && [ ! -f "$DIR/tsconfig.json" ]; do
  DIR=$(dirname "$DIR")
done

if [ ! -f "$DIR/tsconfig.json" ]; then
  echo "$INPUT_JSON"
  exit 0
fi

TSC_OUTPUT=$(cd "$DIR" && npx tsc --noEmit --pretty false 2>&1 || true)
RELEVANT_ERRORS=$(echo "$TSC_OUTPUT" | grep "$FILE_PATH" | head -10 || true)

if [ -n "$RELEVANT_ERRORS" ]; then
  echo "[TypeScript Hook] Type errors detected:" >&2
  echo "$RELEVANT_ERRORS" >&2
fi

echo "$INPUT_JSON"
```

## Prettier Formatter

Hook setup:

```json
{
  "name": "Prettier Auto-Formatter",
  "description": "Format JavaScript and TypeScript files after edits",
  "category": "code-quality",
  "impact": "low",
  "performance": "~500ms per edit",
  "recommended": true,
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "tool == \"Edit\" && tool_input.file_path matches \"\\\\.(ts|tsx|js|jsx)$\"",
        "hooks": [
          {
            "type": "command",
            "command": "bash \"${TEAM_CLAUDE_CONFIG}/optional-hooks/prettier/format-file.sh\""
          }
        ],
        "description": "Auto-format JS/TS files with Prettier after edits"
      }
    ]
  }
}
```

Script:

```bash
#!/bin/bash
set -e

INPUT_JSON=$(cat)
FILE_PATH=$(echo "$INPUT_JSON" | python3 -c '
import json, sys
try:
    data = json.loads(sys.stdin.read())
    print(data.get("tool_input", {}).get("file_path", ""))
except Exception:
    pass
')

if [ -z "$FILE_PATH" ] || [ ! -f "$FILE_PATH" ]; then
  echo "$INPUT_JSON"
  exit 0
fi

if command -v npx >/dev/null 2>&1; then
  npx prettier --write "$FILE_PATH" 2>/dev/null || true
fi

echo "$INPUT_JSON"
```

## Console Detector

```bash
#!/bin/bash
set -e

INPUT_JSON=$(cat)
FILE_PATH=$(echo "$INPUT_JSON" | python3 -c '
import json, sys
try:
    data = json.loads(sys.stdin.read())
    print(data.get("tool_input", {}).get("file_path", ""))
except Exception:
    pass
')

if [ -z "$FILE_PATH" ] || [ ! -f "$FILE_PATH" ]; then
  echo "$INPUT_JSON"
  exit 0
fi

CONSOLE_LOGS=$(grep -n "console\.log" "$FILE_PATH" 2>/dev/null || true)
if [ -n "$CONSOLE_LOGS" ]; then
  echo "[Console Hook] console.log found in $FILE_PATH" >&2
  echo "$CONSOLE_LOGS" | head -5 >&2
fi

echo "$INPUT_JSON"
```

## Session Persistence

Use `SessionStart` to restore context and `SessionEnd` to save it.

```bash
#!/bin/bash
# SessionStart: restore previous branch and recent files.

set -e

SESSION_STATE_DIR="$HOME/.claude/team-session-state"
mkdir -p "$SESSION_STATE_DIR"

CURRENT_DIR=$(pwd)
STATE_FILE="$SESSION_STATE_DIR/$(echo "$CURRENT_DIR" | md5sum | cut -d' ' -f1).json"

if [ -f "$STATE_FILE" ]; then
  PREV_BRANCH=$(jq -r '.branch // "unknown"' "$STATE_FILE" 2>/dev/null || echo "unknown")
  PREV_FILES=$(jq -r '.recent_files[]? // empty' "$STATE_FILE" 2>/dev/null || true)

  echo "[Session] Restoring previous session context" >&2
  if [ "$PREV_BRANCH" != "unknown" ]; then
    echo "[Session] Previous branch: $PREV_BRANCH" >&2
  fi
  if [ -n "$PREV_FILES" ]; then
    echo "[Session] Recently edited files:" >&2
    echo "$PREV_FILES" | head -5 | sed 's/^/  - /' >&2
  fi
fi
```

```bash
#!/bin/bash
# SessionEnd: save current branch and recent files.

set -e

SESSION_STATE_DIR="$HOME/.claude/team-session-state"
mkdir -p "$SESSION_STATE_DIR"

CURRENT_DIR=$(pwd)
STATE_FILE="$SESSION_STATE_DIR/$(echo "$CURRENT_DIR" | md5sum | cut -d' ' -f1).json"
BRANCH=$(git branch --show-current 2>/dev/null || echo "unknown")
RECENT_FILES=$(git diff --name-only HEAD~5..HEAD 2>/dev/null | head -10 || true)

python3 << EOF > "$STATE_FILE"
import json
state = {
    "timestamp": "$(date -u +"%Y-%m-%dT%H:%M:%SZ")",
    "directory": "$CURRENT_DIR",
    "branch": "$BRANCH",
    "recent_files": $(echo "$RECENT_FILES" | jq -R -s -c 'split("\n") | map(select(length > 0))')
}
print(json.dumps(state, indent=2))
EOF

echo "[Session] Session state saved" >&2
```

## Git Push Reminder

```json
{
  "name": "Git Push Reminder",
  "description": "Remind the user to review changes before pushing",
  "category": "git",
  "impact": "low",
  "performance": "instant",
  "recommended": false,
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "tool == \"Bash\" && tool_input.command matches \"git push\"",
        "hooks": [
          {
            "type": "command",
            "command": "bash \"${TEAM_CLAUDE_CONFIG}/optional-hooks/git-reminders/push-reminder.sh\""
          }
        ],
        "description": "Reminder before git push to review changes"
      }
    ]
  }
}
```

```bash
#!/bin/bash
set -e

INPUT_JSON=$(cat)
echo "[Git Hook] Reminder: review changes before pushing" >&2
echo "[Git Hook] Run: git log --oneline -5 && git diff origin/$(git branch --show-current)" >&2
echo "$INPUT_JSON"
```

## Design Notes

- TypeScript and Prettier hooks add latency after edits; enable them per stack.
- Console detection is cheap and useful for JavaScript and TypeScript projects.
- Session persistence should store only safe workspace context, never prompt content or secrets.
