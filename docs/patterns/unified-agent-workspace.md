# Unified Agent Workspace

## Overview

A unified agent workspace gives AI coding agents monorepo-like visibility over the relevant system without requiring every component to live in one Git repository.

The pattern is useful for microservice, platform, and enterprise environments where a change may cross API contracts, services, events, consumers, frontends, infrastructure, and tests. Agents work best when the relevant system is inside one navigable filesystem boundary, but governance, ownership, security, or release independence may still require multiple repositories.

The practical goal is:

```text
Agent-visible workspace
  -> one filesystem boundary
  -> shared instructions
  -> shared maps and workflows
  -> independently versioned source repositories
```

Agentic development has made the unified development workspace more important. A monorepo is the cleanest implementation, but a virtual monorepo can provide many of the same agentic advantages without reorganizing source control. In multi-repo environments, the control plane becomes the explicit replacement for the implicit coordination a monorepo provides.

---

## Problem

Cross-system changes are expensive for agents when the relevant code and knowledge live behind multiple repository, branch, authentication, and tooling boundaries.

Example change:

```text
Customer adds new field
  -> API contract
  -> backend service
  -> emitted event
  -> consuming service
  -> frontend
  -> integration tests
```

In separate repositories, the agent must discover and maintain context across several projects. In a unified workspace, the agent can search, trace, modify, test, and review the blast radius through one filesystem boundary.

---

## Forces

- Agents benefit from whole-system visibility for repository-wide search, dependency tracing, contract updates, and test iteration.
- Humans and organizations may still need repository boundaries for ownership, security, compliance, release cadence, or source access.
- AI-specific knowledge should not pollute production service repositories when it is really cross-system engineering intelligence.
- Generated graphs and documentation are useful as indexes, but source code and deployed artifacts remain the source of truth.
- Tooling should be portable across Claude Code, OpenCode, Copilot, and future agents.
- A central orchestrator should not become an unrestricted super-agent with write access everywhere.
- Service-local knowledge should stay near the service unless governance, security, ownership, or lifecycle constraints require separation.

---

## Solution

Create a developer-control-plane repository that sits above independently governed service repositories and turns them into a coherent agent workspace.

```text
work/
  engineering/
    .git/
    AGENTS.md
    CLAUDE.md
    system.yaml
    maps/
    workflows/
    skills/
    playbooks/
    runbooks/
    generated/
    scripts/

  customer-service/
    .git/

  communications-service/
    .git/

  pricing-service/
    .git/

  schema-registry/
    .git/

  infrastructure/
    .git/
```

Open the parent workspace, not only one service repository. The agent sees a virtual monorepo, while Git still sees independently versioned repositories.

```text
engineering             -> Git repository A
customer-service        -> Git repository B
communications-service  -> Git repository C
pricing-service         -> Git repository D
schema-registry         -> Git repository E
infrastructure          -> Git repository F
```

The root `engineering/AGENTS.md` should explicitly teach agents that the workspace is not a single Git repository:

```markdown
# Engineering Workspace

This directory contains multiple independently versioned repositories.

Before modifying a service:
1. Read its local AGENTS.md.
2. Identify its Git repository.
3. Check its current branch.
4. Never commit changes to multiple repositories as one logical Git operation.

Architecture information is available under:
@engineering/maps/system.md

Event topology:
@engineering/maps/events.md

Service catalogue:
@engineering/system.yaml
```

The same control-plane idea can have two operating modes:

- **Local developer control plane**: a filesystem workspace used by Claude Code, OpenCode, Copilot, and local tools for understanding, navigation, implementation, documentation, and impact analysis.
- **Remote automation control plane**: a central or side repository that runs hosted workflows, dispatches work to target repositories, creates PRs or issues, and aggregates tracking data.

Keep the architectural concept separate from the implementation. Locally, the control plane may be Markdown, YAML, scripts, MCP, and graph tools. Remotely, it may be GitHub Actions, GitHub Agentic Workflows, safe outputs, dispatch workflows, and repo-specific workers.

```text
Engineering control plane
  -> architecture knowledge
  -> dependency / code graph
  -> service catalogue
  -> skills / workflows / standards
  -> local agent workspace
  -> remote automation workflows
```

---

## Three Levels of Truth

Keep source, generated indexes, and human/agent knowledge separate.

```text
Level 1: Source of truth
  service repositories, schemas, Terraform, Helm, production code

Level 2: Engineering index
  service catalogue, dependency graph, event graph, API graph, deployment map

Level 3: Human/agent knowledge
  architecture explanations, runbooks, playbooks, ADRs, workflows, notes
```

Level 1 remains distributed across service repositories. Levels 2 and 3 live mostly in the engineering control-plane repository. Generated knowledge should act as an index into source, not as a replacement for source.

Apply this ownership rule:

```text
Put knowledge beside the smallest scope for which it is true.
```

- Knowledge true about one repository belongs in that repository.
- Knowledge true about several repositories belongs in the control plane.
- Generated cross-repository indexes belong in the control plane.
- Generated repo-local indexes can live in the service repository, local cache, or ignored generated directory depending on tool behavior and governance.

Each repository should contain enough context for an agent to understand and safely change what that repository owns. The control plane should contain enough context for an agent to understand how those ownership boundaries interact.

---

## Service-Local vs Control-Plane Knowledge

Default to keeping single-service knowledge with the service:

```text
customer-service/
  src/
  pom.xml
  AGENTS.md
  docs/
    as-is.md
    to-be.md
    architecture.md
    decisions/
  runbooks/
    production-support.md
  playbooks/
    kafka-lag.md
    database-failure.md
```

This keeps code, docs, operational knowledge, and local instructions changeable in the same PR. It also gives an agent a strong local context boundary.

Put ecosystem knowledge in the control plane:

```text
engineering-control-plane/
  services.yaml
  architecture/
    system-context.md
    domain-map.md
  maps/
    event-topology.md
    service-dependencies.md
  workflows/
    cross-repo-impact-analysis.md
    coordinated-feature-change.md
  standards/
  skills/
  cross-service-runbooks/
  generated/
    dependency-graph.json
    event-topology.json
```

For specifications:

- A service-specific `as-is` or `to-be` spec belongs with the service.
- A cross-service initiative spec belongs in the control plane.
- A service may still contain implementation notes for its part of a cross-service initiative.

For runbooks and playbooks:

- A runbook for diagnosing one service belongs with that service.
- A runbook for diagnosing an end-to-end business flow belongs in the control plane.

Only split one service across separate code, documentation, and operations repositories when organizational boundaries justify the coordination cost.

---

## Control-Plane Repository Contents

### Knowledge

Use this for durable cross-system knowledge:

```text
architecture/
maps/
decisions/
standards/
service-catalog/
terminology/
```

Prefer generating maps from source where possible. Hand-maintain only what source analysis cannot reliably infer, such as domain ownership, criticality, and operational context.

### Agent Instructions

Use this for portable behavioral guidance:

```text
AGENTS.md
CLAUDE.md
instructions/
  java.md
  kafka.md
  testing.md
  security.md
```

The workspace-level instructions define cross-repository safety rules. Service-level instructions define local conventions.

### Workflows

Use this for repeatable engineering procedures:

```text
workflows/
  feature.md
  bug.md
  incident.md
  document-service.md
  analyse-impact.md
```

Versioning workflows is important: when generated documentation or analysis changes, you can trace whether the source changed or the procedure changed.

### Claude Code Hooks

When Claude Code is the active harness, shared hooks can make workspace standards visible at the moment an agent needs them. Keep hook scripts in the control plane when they express cross-repository standards such as task routing, architecture checks, API standards, secrets detection, or session persistence.

Do not treat hooks as the source of truth. Mandatory rules should also exist in CI, repository policy, or pre-commit tooling so non-Claude contributors get the same enforcement. Use [Configure Claude Code Hooks](../playbooks/configure-claude-code-hooks.md) for setup guidance and the [Claude Code Hooks Adapter](../adapters/claude-code-hooks/README.md) for copied hook examples.

### Machine-Generated Knowledge

Use this for indexes and reports that reduce repeated discovery:

```text
generated/
  graphs/
    services.json
    events.json
    dependencies.json
    endpoints.json
  reports/
```

The navigation path becomes:

```text
Question
  -> graph or map
  -> identify relevant repositories
  -> inspect focused source
  -> answer or change
```

instead of:

```text
Question
  -> grep every repository
  -> read broad source
  -> infer architecture repeatedly
```

---

## system.yaml as the Spine

Start with a small service catalogue:

```yaml
services:
  customer:
    repo: ../customer-service
    type: spring-boot
    domain: customer-management
    owner: team-foo
    criticality: high
    produces:
      - CustomerCreated
      - CustomerUpdated
    consumes:
      - ApplicationCreated

  communications:
    repo: ../communications-service
    type: spring-boot
    produces:
      - CommunicationCompleted
    consumes:
      - CustomerCreated
      - CommunicationRequested
```

Over time, generate what can be discovered from source: endpoints, event producers, event consumers, database ownership, and deployment relationships. Keep manual fields for organizational context that source code cannot know.

---

## Relationship to Graph Tools

Repository graph tools are infrastructure for the workspace, not a reason to put every generated artifact into every service repository.

Prefer this shape:

```text
engineering graph
  -> repo-local indexes where useful
  -> service repositories remain clean
```

For example:

```text
Service: communications-service
  CONSUMES -> Event: CommunicationRequested
  PRODUCES -> Event: CommunicationCompleted
  READS -> Database: communications-db
  DEPLOYED_BY -> HelmChart: communications
```

Use [Repository Mental Model Tools](../references/repository-mental-model-tools.md) to decide when Serena, code-review-graph, Graphify, Sense, or generated maps should support this workspace.

Use two graph layers when useful:

```text
System graph
  -> identifies affected repositories
  -> routes work to scoped agents

Repo-local graph
  -> identifies affected symbols and files inside one repository
  -> supports implementation and review
```

The system graph answers "where should I look?". The repo-local graph answers "what code matters inside that repository?".

---

## Remote Automation Control Plane

GitHub's MultiRepoOps and CentralRepoOps patterns validate the same control-plane idea from an automation and governance perspective.

Use a side or central repository when automation should target one or more codebases without storing automation infrastructure in production repositories. This is useful for:

- organization-wide changes
- security patches
- policy rollouts
- configuration standardization
- cross-repository issue tracking
- downstream repository synchronization

Prefer scoped orchestration:

```text
Control-plane orchestrator
  -> filters and prioritizes affected repositories
  -> dispatches repo-specific workers
  -> records decision trail

Repo worker
  -> checks out one target repository
  -> applies focused changes
  -> opens one PR or creates scoped outputs
```

Avoid an unrestricted central agent with broad write access to every repository. Keep orchestrator permissions narrow and delegate repository-specific writes to worker workflows.

This architecture also improves token usage. The orchestrator can use `system.yaml`, maps, and generated graphs to identify affected repositories, then workers can operate with repository-scoped context rather than loading the entire virtual monorepo.

For downstream change propagation:

```text
Source repository change
  -> detect relevant path or contract change
  -> query control-plane graph
  -> identify downstream repositories
  -> dispatch scoped workers
  -> open independent PRs for review
```

This addresses a common repo-per-service problem: a change in one service, schema, or contract may require related updates in external test harnesses, documentation repositories, generated clients, or downstream consumers.

---

## Adoption Path

Do not build a large AI platform before proving the workflow.

Start with Git, Markdown, YAML, and a few scripts:

```text
engineering/
  AGENTS.md
  system.yaml
  maps/
    services.md
    events.md
  workflows/
    document-service.md
    impact-analysis.md
  scripts/
    refresh-maps.sh
```

Then add graph tooling or MCP servers only when repeated discovery becomes a measured bottleneck.

After validating the local workspace, add remote automation gradually:

```text
engineering/
  automation/
    github/
      workflows/
        impact-analysis.md
        downstream-sync.md
        policy-rollout.md
```

Pilot with a small `max` fan-out and low-risk repositories before enabling organization-wide workflows.

---

## When to Use

- You work across multiple services or repositories.
- You need agents to reason about contracts, events, dependencies, or deployment topology.
- A monorepo would help agents but is not organizationally realistic.
- You want portable agent instructions across Claude Code, OpenCode, Copilot, and other clients.
- You need separation between production artifacts and developer intelligence.
- You need a side or central repository for governed cross-repository automation.

## When Not to Use

- One repository already contains the whole relevant system.
- Security or compliance rules prohibit one workspace from seeing all relevant repositories.
- The overhead of maintaining cross-system maps exceeds the value of cross-system agent work.
- You are tempted to build a large knowledge platform before validating a small Markdown/YAML workflow.
- A central automation repository would create broader access than your governance model permits.

---

## Related

- [Context Management](context-management.md)
- [Scheduled Agentic Work](scheduled-agentic-work.md)
- [Repository Exploration](../playbooks/repository-exploration.md)
- [Repository Mental Model Tools](../references/repository-mental-model-tools.md)
- [Grill With Docs](../skills/grill-with-docs.md)
- [Configure Claude Code Hooks](../playbooks/configure-claude-code-hooks.md)
- [Claude Code Hooks Adapter](../adapters/claude-code-hooks/README.md)

---

## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Unified agent workspace / virtual monorepo | User-provided synthesis | Practitioner summary | Describes developer-control-plane repository for multi-repo agentic engineering | N/A |
| MultiRepoOps | GitHub Agentic Workflows | Pattern | Documents side repositories, cross-repository safe outputs, downstream repository changes, and multi-repo automation best practices | https://github.github.com/gh-aw/patterns/multi-repo-ops/ |
| CentralRepoOps | GitHub Agentic Workflows | Pattern | Documents central control repositories, central tracker repositories, orchestrator/worker split, and scoped permissions for multi-repo operations | https://github.github.com/gh-aw/patterns/central-repo-ops/ |
| AGENTS.md as interoperable convention | Configuring Agentic AI Coding Tools paper | Research | Supports repository-level shared instructions, skills, and subagents as cross-ecosystem configuration mechanisms | https://arxiv.org/abs/2602.14690 |
| Repository code graphs and token-efficient exploration | Codebase-Memory paper | Research | Supports persistent Tree-sitter knowledge graphs exposed through MCP for lower-token code exploration | https://arxiv.org/abs/2603.27277 |
| Claude Code workspace hooks | User-provided hook examples | Practitioner examples | Shows how a control plane can host Claude Code prompt routing, post-edit quality checks, session persistence, and pre-commit guardrails | N/A |
