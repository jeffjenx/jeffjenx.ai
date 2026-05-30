# jeffjenx.ai — Enterprise AI Factory

An AI-powered orchestration system for the jeffjenx business ecosystem. Designed as a **navigator and router** for AI agents across multiple platforms (Claude, OpenAI, Copilot, local LLMs), with progressive autonomy, federated data ownership, and consistent personal brand application.

## Architecture Principles

- **Platform Agnostic**: Works with Claude, OpenAI, GitHub Copilot, local LLMs, and custom tools
- **Agent Specialization**: Each namespace is a focused agent with restricted tools and clean context
- **Data Federation**: Projects own their data; queries aggregate without duplication
- **Progressive Autonomy**: Quality-based confidence tiers control human oversight
- **DRY (Don't Repeat Yourself)**: Definitions live in single sources; jeffjenx.ai navigates to them
- **Memory-Driven**: Multi-level CLAUDE.md files capture lessons and rules at global, project, and namespace levels

## Repository Structure

```
jeffjenx.ai/
├── INDEX.md                          # Master navigation hub (start here)
├── namespaces/                       # Agent definitions and routing
│   ├── router.config.yaml            # Central agent routing configuration
│   └── {agent-name}/
│       ├── agent.md                  # Agent definition, tools, handoff protocol
│       ├── jeffjenx.md               # Namespace-specific rules
│       └── tools.json                # Tool definitions (inputs, outputs, constraints)
├── integrations/                     # Platform-specific integration guides
│   ├── claude/README.md
│   ├── openai/README.md
│   ├── copilot/README.md
│   └── local-llms/README.md
├── governance/                       # Autonomy framework and metrics
│   ├── autonomy-framework.md         # Confidence tiers, checkpoints, scoring
│   └── metrics.md                    # How to measure agent performance
├── tools/                            # CLI utilities and interfaces
│   ├── review-ui/                    # MVP approval interface
│   ├── init-project.sh               # Set up project to use jeffjenx.ai
│   ├── log-decision.sh               # Log decisions to memory
│   └── validate-agent.sh             # Validate agent definitions
└── .gitignore
```

## Quick Start

1. **Read [INDEX.md](INDEX.md)** — Master navigation to all resources
2. **Review [governance/autonomy-framework.md](governance/autonomy-framework.md)** — Understand confidence tiers and checkpoints
3. **Explore [namespaces/](namespaces/)** — See available agents and their capabilities
4. **Check integration guides** — Learn how to connect your AI platform

## Key Concepts

### Agents (Specialized AI Workers)
Each agent has:
- **Single focused job** (e.g., Copywriter, Researcher, Validator)
- **Own clean context** (only data it needs)
- **Tool restrictions** (cannot edit code if it's a copywriter)
- **Clear handoff protocol** (structured summary for next agent)

### Multi-Level Memory (CLAUDE.md Pattern)
Three levels load in order (later overrides earlier):
- **Global** (`c:\Development\jeffjenx.md`): Org-wide facts, values, commands
- **Project** (in project repo): Project-specific conventions
- **Namespace** (in agent folder): Domain-specific rules

### Autonomy Tiers
- **Tier 0 (Learning)**: 3 checkpoints required (story → brief → deploy)
- **Tier 1 (Trusted)**: 2 checkpoints (brief → deploy)
- **Tier 2 (Expert)**: 1 checkpoint (deploy only)
- **Tier 3 (Autonomous)**: 0 checkpoints; async validation

### Data Federation
- **jeffjenx.db** (separate repo): Queries across all projects; owns no data itself
- **Project repos**: Own their data (CSV, JSON, local files)
- Result: Single source of query; no duplication

## Integration Points

- **jeffjenx.org**: Organizational memory, decisions, guidelines
- **jeffjenx.db**: Data aggregation layer
- **jeffjenx.design**: Brand & style (reference only)
- **Project repos** (.com, .net, .design, .me, .info): Living implementations

## Feedback & Evolution

This system is designed to learn and improve. When agents make mistakes:
1. Identify the mistake
2. Add a rule to the relevant jeffjenx.md file
3. Future runs benefit from the rule
4. Over time, jeffjenx.md becomes a record of all lessons learned

## Current Status

**Phase 1: Foundation** (In Progress)
- Repository structure: ✅
- Core documentation: In progress
- Agent definitions: Pending
- Integration guides: Pending
- Autonomy framework: In progress
- Multi-level memory: Pending

See [governance/autonomy-framework.md](governance/autonomy-framework.md) for detailed roadmap.
