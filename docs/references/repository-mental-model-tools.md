# Repository Mental Model Tools

## Overview

Repository mental model tools help humans and AI agents understand a codebase at repository scope. They reduce repeated rediscovery of architecture, dependencies, symbol relationships, and documentation by precomputing or presenting a map of the system.

They are adjacent to [Repository Exploration](../playbooks/repository-exploration.md), [Context Management](../patterns/context-management.md), and [Agent Context Artifacts](agent-context-artifacts.md): instead of repeatedly grepping, reading files, and reconstructing architecture from scratch, these tools provide a persistent or queryable model that can guide exploration.

For multi-agent development, evaluate these tools by portability and token efficiency before visualization. The strongest setup is not a vendor-specific dashboard; it is a portable repository substrate that lets Copilot, Claude Code, OpenCode, and other agents share the same instructions and code intelligence.

---

## The Problem

Normal AI-assisted repository exploration often works like this:

```text
Question
  -> search / grep / embeddings
  -> find likely files
  -> read files
  -> reconstruct architecture
  -> answer or edit
```

That can work, but it becomes expensive and unreliable in large systems. Agents repeatedly rediscover relationships such as:

```text
Controller -> Service -> Repository -> Database
Service -> Outbox -> Kafka publisher
DTO -> Mapper -> Domain -> Entity
```

Repository-level graph and code-intelligence tools try to calculate part of that map once, then let humans or agents navigate it deliberately.

The goal is not to make every agent ingest the whole repository. The goal is to externalize durable repository knowledge so each agent can retrieve the smallest relevant context for the current task.

```text
                  Repository
                      |
          +-----------+-----------+
          |                       |
  Shared instructions      Code intelligence
  AGENTS.md / skills             MCP
          |                       |
          +-----------+-----------+
                      |
        +-------------+-------------+
        |             |             |
     Copilot     Claude Code     OpenCode
        |             |             |
        +-------------+-------------+
                      |
              interchangeable agents
```

---

## Tool Families

### Portable Substrate

The first layer is not a graph product. It is the repository-level contract that every agent can consume:

- **AGENTS.md** for repository instructions, vocabulary, constraints, and maintenance rules.
- **CONTEXT.md** for durable system, domain, architecture, or feature understanding.
- **Skills** for executable or repeatable agent capabilities that should survive across harnesses.
- **MCP** for exposing local tools and code intelligence through a common protocol.

This substrate lets the repository remain the source of truth instead of duplicating guidance across Claude-specific, Copilot-specific, and OpenCode-specific configuration. It also makes model choice more flexible: use expensive frontier models for hard reasoning, cheaper models for routine work, and local deterministic tools for code structure.

### Human Learning Tools

These tools primarily help developers understand an unfamiliar repository through generated docs, diagrams, summaries, or interactive exploration.

Examples:
- **Understand Everything** generates recursive README-style explanations and a browsable documentation site.
- **Understand Anything** builds an interactive knowledge graph and dashboard for learning architecture.
- **DeepWiki / OpenDeepWiki / RepoWiki-style tools** generate repository documentation, diagrams, and codemaps.

Use these when the main goal is onboarding, architectural orientation, or explaining a repository to humans.

### Agent Context Tools

These tools primarily give coding agents a structural map they can query or traverse instead of relying only on raw-file retrieval.

Examples:
- **Graphify** builds a repository knowledge graph across code, docs, schemas, configs, and other artifacts. For source code, it emphasizes deterministic local AST parsing with Tree-sitter and distinguishes extracted relationships from inferred ones.
- **CodeGraph** precomputes symbols, calls, dependencies, and call paths for task-relevant retrieval and blast-radius analysis.
- **code-review-graph** builds a persistent Tree-sitter structural map, tracks changes incrementally, and exposes precise context through MCP.

Use these when the main goal is to reduce token consumption and improve an agent's ability to answer structural questions such as "what calls this?", "what depends on this?", and "what might break if this changes?".

### Code Intelligence Tools

These tools are closer to IDE or language-server capabilities for agents: symbol navigation, references, inheritance, imports, semantic editing, conventions, and blast-radius analysis.

Examples:
- **Serena** provides symbol-aware code navigation and editing through MCP-style capabilities.
- **Sense** provides an MCP server with symbol graph, call relationships, inheritance, imports, semantic search, conventions, and blast-radius analysis.

Use these when the main goal is precise coding support in a large codebase: finding references, editing symbols, understanding blast radius, and avoiding unnecessary file reads.

---

## Graphs vs. Traditional RAG

Traditional repository RAG usually works like this:

```text
code -> chunks -> embeddings -> vector database -> similarity search
```

That retrieves text that appears semantically related to a question. Repository graph tools instead expose structural relationships that can be traversed:

```text
ApplicationArchiver
  -> calls ApplicationRepository.archive()
  -> updates applications.archived_at
```

This supports questions that are hard to answer with similarity search alone:

- What calls this symbol?
- What does this component depend on?
- What depends on this component?
- How do I get from this endpoint to this table?
- Which components are central?
- What is the likely blast radius of changing this function?

The two approaches can complement each other. Semantic search finds conceptually related text; repository graphs and code intelligence expose structural relationships.

---

## Selection Heuristics

Evaluate these tools by the job you need them to do:

| Need | Prefer |
|---|---|
| Onboard a human to an unfamiliar repo | Understand Anything, Understand Everything, DeepWiki-style tools |
| Give an AI agent an architecture map | Graphify, CodeGraph, code-review-graph |
| Give an AI agent IDE-like symbol operations | Serena, Sense |
| Reduce repeated file reads and token use | Graphify, code-review-graph, Sense, Serena |
| Analyze blast radius before editing | Sense, CodeGraph, code-review-graph, Serena |
| Explore cross-artifact relationships across code, docs, schemas, and config | Graphify |

For OpenCode, Claude Code, Codex, Cursor, Copilot, and Gemini CLI workflows, prioritize whether the tool improves the agent's ability to navigate the repository without repeatedly loading large amounts of irrelevant context. Graph visualization is useful, but agent-accessible navigation and precise retrieval matter more for implementation work.

Rank tools by:

1. **Portability**: can the same repository setup work across multiple agents and harnesses?
2. **Token efficiency**: does the tool reduce broad file reads and repeated rediscovery?
3. **Multi-agent value**: can several agents reuse the same externalized knowledge?
4. **Visualization**: does it help humans see the system?

Do not start by assembling many MCP servers. Each server and tool definition has a context cost and can degrade tool selection. Prefer the minimum effective toolset: add a tool when it solves a measured repository-navigation, change-analysis, or comprehension problem.

---

## Learning Order

For a vendor-neutral, token-conscious development workflow, learn these in order:

| Rank | Tool / concept | Why it comes here |
|---|---|---|
| 1 | MCP + AGENTS.md + skills | Establishes the portable substrate before choosing products |
| 2 | Serena | Gives agents IDE-like symbol navigation and editing through MCP |
| 3 | code-review-graph | Adds persistent structural memory and change-impact context |
| 4 | Graphify | Adds architectural and cross-artifact knowledge graphs |
| 5 | Sense | Evaluate after Serena and code-review-graph because there is overlap |
| 6 | Understand Anything | Useful for human learning and legacy-code orientation |
| 7 | Understand Everything | Useful for generated documentation, but less critical to multi-agent infrastructure |

The core progression is:

```text
1. Portable instructions and tools
   AGENTS.md + skills + MCP

2. Symbol-level code intelligence
   Serena

3. Persistent repository memory
   code-review-graph

4. Architecture and cross-artifact map
   Graphify

5. Multi-agent orchestration
   Planner / Implementer / Reviewer sharing the same MCP layer
```

This order favors agent portability and token economy. Visualization becomes valuable after agents can already navigate code precisely.

---

## Practical Combinations

These tools can overlap. Avoid installing several just because they all look useful. Combine them when their roles are distinct:

```text
Repository
  -> Graphify: architecture map and cross-artifact relationships
  -> Serena or Sense: precise symbol navigation and editing support
  -> Agent: focused context, fewer irrelevant file reads
```

Use a human-learning tool such as Understand Anything when you personally need to learn the system. Use an agent-context or code-intelligence tool when the agent needs to perform reliable repository-level coding work.

For a small, high-leverage setup, start with:

```text
Repository
  -> AGENTS.md and skills: portable instructions
  -> MCP: portable tool interface
  -> Serena: symbol-level navigation and edits
  -> code-review-graph: structural relationships and change impact
  -> Agent: focused context, fewer broad reads
```

Then add Graphify when architecture, documentation, schema, and configuration relationships become a recurring bottleneck.

Avoid this pattern:

```text
Agent A -> rereads repository
Agent B -> rereads repository
Agent C -> rereads repository
```

Prefer this pattern:

```text
Shared code intelligence
  -> Agent A: implementation
  -> Agent B: review
  -> Agent C: architecture check
```

Agents should share intelligence rather than duplicate context.

---

## Token-Budget Architecture

A cost-conscious solo-developer setup should reserve expensive models for reasoning and use local deterministic analysis for repository structure:

```text
Expensive model
  -> hard reasoning and orchestration

Cheaper or local models
  -> routine coding, tests, docs

Shared context layer
  -> AGENTS.md / skills
  -> MCP
  -> Serena
  -> code-review-graph
  -> Graphify when needed
```

The optimization target is not maximum standalone model intelligence. It is useful development work per token and subscription cost.

---

## Relationship to Handbook Practices

- [Repository Exploration](../playbooks/repository-exploration.md): these tools can accelerate module mapping, dependency discovery, and architecture exploration.
- [Agent Context Artifacts](agent-context-artifacts.md): `AGENTS.md`, `CONTEXT.md`, skills, and task state define what durable knowledge should exist before graph or code-intelligence tools are added.
- [Context Management](../patterns/context-management.md): these tools help select the small subset of repository context relevant to the task.
- [Unified Agent Workspace](../patterns/unified-agent-workspace.md): these tools can provide the shared code intelligence layer for a virtual monorepo or developer-control-plane repository.
- [Scheduled Agentic Work](../patterns/scheduled-agentic-work.md): recurring maintenance jobs can use graphs and code intelligence to expand from changed files to affected components without scanning the whole repository every run.
- [Grill With Docs](../skills/grill-with-docs.md): repository graphs and code intelligence can supply facts during grilling so the user only needs to make decisions.
- [Ralph Loop](../patterns/ralph-loop/README.md): implementation loops benefit when each iteration can retrieve precise affected code rather than rereading broad repository context.

---

## Sources and Provenance

| Concept/Section | Source | Type | Context | URL |
|---|---|---|---|---|
| Repository-level code graph research | RepoGraph paper | Research | Supports using repository-level structural graphs for AI software engineering tasks | https://arxiv.org/abs/2410.14684 |
| Graphify | Graphify-Labs `graphify` | Tool/Repository | Repository knowledge graph for agent context using local AST parsing and explainable relationships | https://github.com/Graphify-Labs/graphify |
| Understand Everything | Mor-Li `understand-everything` | Tool/Repository | AI-generated repository explanations and documentation site | https://github.com/Mor-Li/understand-everything |
| Understand Anything | Lum1104 `Understand-Anything` | Tool/Repository | Interactive knowledge graph for learning and exploring codebases | https://github.com/Lum1104/Understand-Anything |
| Serena | oraios `serena` | Tool/Repository | MCP-style symbol-aware coding toolkit | https://github.com/oraios/serena |
| Sense | luuuc `sense` | Tool/Repository | MCP server for symbol graph, calls, inheritance, semantic search, conventions, and blast-radius analysis | https://github.com/luuuc/sense |
| code-review-graph | tirth8205 `code-review-graph` | Tool/Repository | Local-first structural map for MCP and CLI with incremental context selection | https://github.com/tirth8205/code-review-graph |
| CodeGraph | colbymchenry `codegraph` | Tool/Repository | Code graph for symbols, calls, dependencies, task-relevant retrieval, and blast-radius analysis | https://github.com/colbymchenry/codegraph |
| DeepWiki-style tooling | AsyncFuncAI `deepwiki-open` | Tool/Repository | Repository wiki, diagrams, and codemap generation | https://github.com/AsyncFuncAI/deepwiki-open |
| AGENTS.md as interoperable convention | Configuring Agentic AI Coding Tools paper | Research | Supports repository-level shared instructions, skills, and subagents as cross-ecosystem configuration mechanisms | https://arxiv.org/abs/2602.14690 |
| Codebase-Memory | Codebase-Memory paper | Research | Supports persistent Tree-sitter knowledge graphs exposed through MCP for lower-token code exploration | https://arxiv.org/abs/2603.27277 |
| Learning order and token-budget architecture | User-provided synthesis | Practitioner summary | Prioritizes MCP, AGENTS.md, Serena, code-review-graph, Graphify, Sense, and human-learning tools by portability, token efficiency, and multi-agent value | N/A |
