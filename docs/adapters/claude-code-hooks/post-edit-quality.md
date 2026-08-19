# Claude Code Hook Examples: Post-Edit Quality

## Purpose

Use post-edit hooks to surface relevant standards after Claude modifies code. These examples should usually be advisory because design and architecture checks can produce false positives.

## SOLID Reminder

```bash
#!/bin/bash
# Post-code hook: SOLID principles reminder for Java changes.

set -euo pipefail

CHANGED_FILES=$(git diff --name-only 2>/dev/null | grep -E "\.java$" || true)
if [ -z "$CHANGED_FILES" ]; then
  CHANGED_FILES=$(git diff --cached --name-only 2>/dev/null | grep -E "\.java$" || true)
fi

if [ -z "$CHANGED_FILES" ]; then
  exit 0
fi

DIFF_CONTENT=$(git diff 2>/dev/null; git diff --cached 2>/dev/null)

echo "SOLID principles checklist"
echo "1. SRP: does each class have one reason to change?"
echo "2. OCP: can behavior be extended without modifying existing code?"
echo "3. LSP: are subtypes substitutable for base types?"
echo "4. ISP: are interfaces focused and cohesive?"
echo "5. DIP: do high-level policies depend on abstractions?"

if echo "$DIFF_CONTENT" | grep -qE "switch.*\.(getType|getClass)"; then
  echo "Warning: type switching detected. Consider polymorphism."
fi

if echo "$DIFF_CONTENT" | grep -qE "new.*Repository|new.*Service"; then
  echo "Warning: direct construction detected. Consider dependency injection."
fi

exit 0
```

## Domain Review

```bash
#!/bin/bash
# Post-code hook: DDD reminders for domain model changes.

set -euo pipefail

CHANGED_FILES=$(git status --porcelain 2>/dev/null | awk '{print $NF}')

if echo "$CHANGED_FILES" | grep -qE "(Entity\.java|Aggregate\.java|ValueObject\.java)"; then
  echo "Domain model changes detected. Check:"
  echo "  - Entities have identity and lifecycle."
  echo "  - Value objects are immutable."
  echo "  - Aggregates enforce consistency boundaries."
  echo "  - Domain events are published for state changes."
  echo "  - Business logic lives in the domain model, not services."
fi

if echo "$CHANGED_FILES" | grep -qE "Repository\.java"; then
  echo "Repository changes detected. Check:"
  echo "  - Repositories work with aggregate roots only."
  echo "  - Repositories return domain objects, not persistence entities."
  echo "  - Complex queries use specifications or explicit query objects."
fi

if echo "$CHANGED_FILES" | grep -qE "(Service\.java|UseCase\.java)"; then
  echo "Application service changes detected. Check:"
  echo "  - Services orchestrate use cases and avoid business rules."
  echo "  - Transaction boundaries are deliberate."
  echo "  - Cross-aggregate coordination is explicit."
fi

exit 0
```

## API Standards Check

```bash
#!/bin/bash
# Post-code hook: REST API standards check for Spring controllers.

set -euo pipefail

DIFF_CONTENT=$(git diff 2>/dev/null; git diff --cached 2>/dev/null)

if ! echo "$DIFF_CONTENT" | grep -qE "@(RestController|Controller|RequestMapping|GetMapping|PostMapping|PutMapping|DeleteMapping|PatchMapping)"; then
  exit 0
fi

echo "API standards check"

for header in Correlation-Id Session-Id Request-Id User-Ref Channel; do
  if ! echo "$DIFF_CONTENT" | grep -q "@RequestHeader.*$header"; then
    echo "Warning: request header may be missing: $header"
  fi
done

if echo "$DIFF_CONTENT" | grep -qE '@RequestHeader.*"X-'; then
  echo "Warning: deprecated X- header prefix detected. Prefer unprefixed custom headers."
fi

for header in Trace-Id Timestamp; do
  if ! echo "$DIFF_CONTENT" | grep -qE "\.header\(\"$header\""; then
    echo "Warning: response header may be missing: $header"
  fi
done

if echo "$DIFF_CONTENT" | grep -qE '"traceId"\s*:|"timestamp"\s*:'; then
  echo "Warning: metadata may be in the response body. Prefer headers for trace and timestamp metadata."
fi

if echo "$DIFF_CONTENT" | grep -qE "HttpStatus\.(BAD_REQUEST|UNAUTHORIZED|FORBIDDEN|NOT_FOUND)" && \
   echo "$DIFF_CONTENT" | grep -qE '"type".*"TECHNICAL"'; then
  echo "Warning: 4xx errors should usually be classified as BUSINESS, not TECHNICAL."
fi

exit 0
```

## Design Notes

- These checks are intentionally reminders, not a replacement for code review.
- Make output short enough for Claude to act on inside the current transcript.
- Link the warning to a canonical standard or skill when the team has one.
