# Claude Code Hook Examples: Commit Safety

## Purpose

Use commit-safety hooks for checks that inspect staged changes. These hooks may be blocking when the signal is reliable, such as staged secrets. For team-wide enforcement, mirror mandatory checks in CI or normal Git pre-commit tooling.

## Test Coverage Check

```bash
#!/bin/bash
# Pre-commit hook: require tests or coverage evidence for Java source changes.

set -euo pipefail

COVERAGE_THRESHOLD=80
JAVA_FILES=$(git diff --cached --name-only --diff-filter=AM | grep "\.java$" | grep -v "Test\.java$" || true)

if [ -z "$JAVA_FILES" ]; then
  exit 0
fi

MISSING_TESTS=()

for file in $JAVA_FILES; do
  if echo "$file" | grep -qE "(Config\.java|DTO\.java|Configuration\.java|Mapper\.java|Constants\.java|Application\.java)"; then
    continue
  fi

  test_file=$(echo "$file" | sed 's/src\/main\/java/src\/test\/java/' | sed 's/\.java$/Test.java/')
  if [ ! -f "$test_file" ]; then
    MISSING_TESTS+=("$test_file")
  fi
done

if [ ${#MISSING_TESTS[@]} -gt 0 ]; then
  echo "Missing test files:"
  printf '  - %s\n' "${MISSING_TESTS[@]}"
fi

JACOCO_REPORT=""
for report_path in \
  "target/site/jacoco/jacoco.xml" \
  "target/jacoco/jacoco.xml" \
  "build/reports/jacoco/test/jacocoTestReport.xml"; do
  if [ -f "$report_path" ]; then
    JACOCO_REPORT="$report_path"
    break
  fi
done

if [ -n "$JACOCO_REPORT" ]; then
  missed=$(grep -o 'type="LINE" missed="[0-9]*"' "$JACOCO_REPORT" | tail -1 | grep -o '[0-9]*' || echo "0")
  covered=$(grep -o 'type="LINE" missed="[0-9]*" covered="[0-9]*"' "$JACOCO_REPORT" | tail -1 | grep -o 'covered="[0-9]*"' | grep -o '[0-9]*' || echo "0")
  total=$((missed + covered))

  if [ "$total" -gt 0 ]; then
    coverage=$((covered * 100 / total))
    if [ "$coverage" -lt "$COVERAGE_THRESHOLD" ]; then
      echo "Coverage below threshold: ${coverage}% < ${COVERAGE_THRESHOLD}%"
      exit 1
    fi
  fi
else
  echo "No JaCoCo report found. Run tests and generate coverage before relying on this check."
fi

exit 0
```

## Secrets Detection

```bash
#!/bin/bash
# Pre-commit hook: block common hardcoded secrets.

set -euo pipefail

STAGED_DIFF=$(git diff --cached --diff-filter=AM 2>/dev/null)
if [ -z "$STAGED_DIFF" ]; then
  exit 0
fi

SECRETS_FOUND=0

if echo "$STAGED_DIFF" | grep -nE '^\+.*((password|passwd|pwd)\s*[=:]\s*"[^"${}]+")' | grep -vE '(//.*|/\*.*|\*.*|#.*)'; then
  echo "Hardcoded password detected."
  SECRETS_FOUND=$((SECRETS_FOUND + 1))
fi

if echo "$STAGED_DIFF" | grep -nE '^\+.*(AKIA[0-9A-Z]{16}|aws_secret_access_key\s*[=:])'; then
  echo "AWS credential detected."
  SECRETS_FOUND=$((SECRETS_FOUND + 1))
fi

if echo "$STAGED_DIFF" | grep -nE '^\+.*(api[_-]?key|api[_-]?secret|access[_-]?token|auth[_-]?token)\s*[=:]\s*"[^"${}]+"' | grep -vE '(//.*|/\*.*|\*.*|#.*|example|placeholder|TODO)'; then
  echo "API key or token detected."
  SECRETS_FOUND=$((SECRETS_FOUND + 1))
fi

if echo "$STAGED_DIFF" | grep -nE '^\+.*(BEGIN (RSA |EC |DSA |OPENSSH )?PRIVATE KEY)'; then
  echo "Private key detected."
  SECRETS_FOUND=$((SECRETS_FOUND + 1))
fi

if echo "$STAGED_DIFF" | grep -nE '^\+.*(jdbc|postgresql|mysql|mongodb|redis)://[^:]+:[^@${}]+@' | grep -vE '\$\{'; then
  echo "Connection string with embedded credentials detected."
  SECRETS_FOUND=$((SECRETS_FOUND + 1))
fi

SENSITIVE_FILES=$(git diff --cached --name-only 2>/dev/null | grep -iE "(\.env$|\.env\.|credentials|\.pem$|\.key$|\.p12$|\.jks$|\.keystore$)" || true)
if [ -n "$SENSITIVE_FILES" ]; then
  echo "Sensitive files staged:"
  echo "$SENSITIVE_FILES" | sed 's/^/  - /'
  SECRETS_FOUND=$((SECRETS_FOUND + 1))
fi

if [ "$SECRETS_FOUND" -gt 0 ]; then
  echo "Blocked: remove secrets, use environment variables, or store values in a secrets manager."
  exit 1
fi

exit 0
```

## Architecture Check

```bash
#!/bin/bash
# Pre-commit hook: detect clean architecture and SOLID violations.

set -euo pipefail

ENFORCEMENT_MODE="${ARCHITECTURE_ENFORCEMENT:-advisory}"
VIOLATIONS=0
BLOCKING_VIOLATIONS=0

if git diff --cached --diff-filter=AM | grep -E "^[+].*import.*javax\.persistence\.|^[+].*import.*org\.springframework\.(data|beans|context|transaction)"; then
  DOMAIN_FILES=$(git diff --cached --name-only | grep -E "domain/model/.*\.java$" || true)
  if [ -n "$DOMAIN_FILES" ]; then
    echo "Layer violation: domain model depends on persistence or Spring infrastructure."
    VIOLATIONS=$((VIOLATIONS + 1))
    BLOCKING_VIOLATIONS=$((BLOCKING_VIOLATIONS + 1))
  fi
fi

if git diff --cached --diff-filter=AM | grep -E "^[+].*import.*infrastructure\."; then
  APPLICATION_FILES=$(git diff --cached --name-only | grep -E "application/.*\.java$" || true)
  if [ -n "$APPLICATION_FILES" ]; then
    echo "Layer violation: application layer depends directly on infrastructure."
    VIOLATIONS=$((VIOLATIONS + 1))
    BLOCKING_VIOLATIONS=$((BLOCKING_VIOLATIONS + 1))
  fi
fi

if git diff --cached --diff-filter=AM | grep -E "switch.*\.(getType|getClass|getKind)\(\)|if.*==.*Type\."; then
  echo "Architecture smell: type switching may violate Open/Closed Principle."
  VIOLATIONS=$((VIOLATIONS + 1))
fi

DOMAIN_FILES=$(git diff --cached --name-only | grep -E "domain/.*\.java$" || true)
for file in $DOMAIN_FILES; do
  if [ -f "$file" ] && grep -qE "^import.*(application|infrastructure|api|controller)" "$file"; then
    echo "Dependency rule violation: domain depends on an outer layer in $file."
    VIOLATIONS=$((VIOLATIONS + 1))
    BLOCKING_VIOLATIONS=$((BLOCKING_VIOLATIONS + 1))
    break
  fi
done

if [ "$VIOLATIONS" -gt 0 ]; then
  echo "Architecture review required: $VIOLATIONS issue(s), $BLOCKING_VIOLATIONS blocking."
  if [ "$ENFORCEMENT_MODE" = "blocking" ] && [ "$BLOCKING_VIOLATIONS" -gt 0 ]; then
    exit 1
  fi
fi

exit 0
```

## Design Notes

- Secrets detection is a strong default for blocking mode.
- Coverage and architecture checks should start advisory unless the team has already tuned the rules.
- Prefer staged-diff checks for commit hooks so the result matches what will be committed.
