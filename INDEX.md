# jeffjenx.ai — Master Index

**Your AI Factory Navigation Hub** — Start here. This index is a *navigator*, not a warehouse. All definitions and data live elsewhere; this document points you in the right direction.

---

## 🚀 Quick Start (5 minutes)

1. **New to jeffjenx.ai?** → Read [📖 Overview](#overview) (5 min)
2. **Want to understand autonomy?** → See [⚙️ Autonomy Tiers](#autonomy-tiers) (3 min)
3. **Ready to use an agent?** → Find your task below, then consult its agent page
4. **Adding a new capability?** → Go to [🏗️ Architecture](#architecture) (10 min)

---

## 📖 Overview

**What is jeffjenx.ai?**
- AI agent orchestration system for the jeffjenx business ecosystem
- Platform-agnostic (works with Claude, OpenAI, Copilot, local LLMs)
- Progressive autonomy (humans → oversight → autonomous operation)
- Federated data (projects own data; jeffjenx.db queries across)
- Memory-driven (mistakes become rules; rules prevent future mistakes)

**Key Files to Know**:
- **[README.md](README.md)** — Full project overview
- **[governance/autonomy-framework.md](governance/autonomy-framework.md)** — Confidence tiers and how they work
- **[namespaces/router.config.yaml](namespaces/router.config.yaml)** — Agent definitions and routing

---

## ⚙️ Autonomy Tiers

How agents graduate from supervised to autonomous:

| Tier | Checkpoints | Human Role | Example |
|------|------------|-----------|---------|
| **0: Learning** | 3 (story → brief → review) | Full oversight; approves each step | New agents; high-risk tasks |
| **1: Trusted** | 2 (brief → review) | Spot-checks; approves brief before build | Proven agents; moderate-risk |
| **2: Expert** | 1 (review only) | Reviews output after completion | High-confidence; routine tasks |
| **3: Autonomous** | 0 (async) | Periodic audits; optional oversight | Proven track record; predictable |

**[Full autonomy framework →](governance/autonomy-framework.md)**

---

## 👥 Available Agents

### By Task Type

#### Content Creation (Brand-Consistent Writing)
- **Copywriting Agent** — Product descriptions, social posts, email copy
  - Confidence tier starting point: **Tier 0**
  - Tools: Read (data + brand), Query (jeffjenx.db)
  - Cannot: Edit code, change brand guidelines
  - **[Agent details →](namespaces/copywriting/agent.md)** *(in progress)*

#### Data Research & Mapping
- **Researcher Agent** — Explore codebase, document patterns, flag risks
  - Confidence tier starting point: **Tier 0**
  - Tools: Read, Grep, Glob
  - Cannot: Edit files, run commands
  - **[Agent details →](namespaces/researcher/agent.md)** *(in progress)*

#### Requirements & Planning
- **Story Writer** — Convert ideas into user stories with acceptance criteria
  - Confidence tier starting point: **Tier 0**
  - Checkpoint: **Human must approve story before proceeding**
  - Cannot: Invent business rules, write code
  - **[Agent details →](namespaces/story-writer/agent.md)** *(in progress)*

- **Spec Writer** — Technical blueprints (data models, APIs, UI changes)
  - Confidence tier starting point: **Tier 0**
  - Checkpoint: **Human must approve spec before building**
  - Cannot: Edit files, invent infrastructure
  - **[Agent details →](namespaces/spec-writer/agent.md)** *(in progress)*

#### Implementation & Execution
- **Builder Agent** — Execute technical specs (domain-specific)
  - Confidence tier starting point: **Tier 0**
  - Tools: Read, Edit, Write, Bash (scoped to domain)
  - Cannot: Modify outside agreed scope
  - **[Agent details →](namespaces/builder/agent.md)** *(in progress)*

#### Quality Assurance & Auditing
- **Validator Agent** — Post-implementation audit (never fixes, only reports)
  - Confidence tier starting point: **Tier 0**
  - Audits: Acceptance criteria ✓ | Spec compliance ✓ | Brand consistency ✓ | Security ✓
  - Checkpoint: **Human reviews report; approves or flags for rework**
  - **[Agent details →](namespaces/validator/agent.md)** *(in progress)*

---

## 🔀 Typical Workflows

### Workflow 1: Simple Task (e.g., "Generate product copy")

```
YOU: "Create descriptions for 3 new products"
  ↓
[Researcher] Maps products.csv structure ← jeffjenx.com data
[Story] Creates acceptance criteria (tone, length, SEO metadata)
⏸ YOU APPROVE: "Looks good"
[Spec] Blueprints: "Use copywriting-agent to generate descriptions"
⏸ YOU APPROVE: "Go ahead"
[Builder/Copywriting] Generates 3 descriptions using brand voice from jeffjenx.design
[Validator] Checks: Tone ✓ | Length ✓ | Brand consistency ✓
⏸ YOU REVIEW: "Ship it"
[DONE] Descriptions committed to project
```

### Workflow 2: Complex Task (e.g., "Build invoice reminder feature")

```
YOU: "Add invoice reminders for unpaid invoices >7 days"
  ↓
[Researcher] Maps invoice, payment, email code; flags multi-tenant risks
[Story] Creates acceptance criteria + failure paths + edge cases
⏸ YOU APPROVE: Story looks complete
[Spec] Blueprints: New Job + API endpoints + Frontend UI changes + Tests needed
⏸ YOU REVIEW: "I want to use BullMQ for jobs, not cron"
[Spec] Updates brief; you re-approve
[Builder/Backend] Implements API + BullMQ job + unit tests
[Builder/Frontend] Builds admin UI tile + reminder button; reads API summary from Backend
[Validator] Checks: All acceptance criteria ✓ | Tests ✓ | No tenant isolation gaps ✓
⏸ YOU REVIEW: Validator report shows 1 minor finding
[DONE] Feature ships
```

---

## 📊 Data & Queries

**Need data across projects?** → Use **[jeffjenx.db](../jeffjenx.db/)**

Quick reference:

| Data | Source | Query | Output |
|------|--------|-------|--------|
| Products | jeffjenx.com | `SELECT * FROM products WHERE category='nes'` | JSON/CSV |
| Inventory | jeffjenx.info | `SELECT * FROM inventory LIMIT 10` | JSON/CSV |
| Career/Resume | jeffjenx.me | `SELECT * FROM portfolio WHERE type='experience'` | JSON |
| Brand tokens | jeffjenx.design | (reference only; no queries) | CSS/JSON |

**[Data warehouse docs →](../jeffjenx.db/README.md)**

---

## 🏛️ Organization & Memory

**Where to find organizational info?**

- **Business rules & guidelines** → [jeffjenx.org/guidelines/](../jeffjenx.org/guidelines/)
- **Historical decisions** → [jeffjenx.org/decisions/](../jeffjenx.org/decisions/)
- **Audit log & lessons** → [jeffjenx.org/memory/](../jeffjenx.org/memory/)
- **Domain glossary** → [jeffjenx.org/definitions/domain-glossary.md](../jeffjenx.org/definitions/domain-glossary.md)

**[Organizational hub →](../jeffjenx.org/README.md)**

---

## 🎨 Brand & Style

**Source of truth for brand, style, design:**

- **Brand strategy** → [jeffjenx.design/brand-strategy.html](../../jeffjenx.design/jeffjenx-brand-strategy.html)
- **Style guide** → [jeffjenx.design/style-guide.html](../../jeffjenx.design/jeffjenx-style-guide.html)
- **Design tokens** → [jeffjenx.design/jeffjenx-tokens.css](../../jeffjenx.design/jeffjenx-tokens.css)
- **Icons** → [jeffjenx.design/icons/](../../jeffjenx.design/icons/)

**This is the ONLY place for brand/style. jeffjenx.ai agents reference it; never duplicate.**

---

## 🔌 Platform Integrations

**Using Claude, OpenAI, Copilot, or local LLMs?**

| Platform | Integration Guide | Notes |
|----------|-------------------|-------|
| **Claude** (API) | [integrations/claude/](integrations/claude/) | Recommended for complex reasoning |
| **OpenAI** (GPT-4) | [integrations/openai/](integrations/openai/) | Alternative for code tasks |
| **GitHub Copilot** | [integrations/copilot/](integrations/copilot/) | Built into VS Code; convenient |
| **Local LLMs** (Ollama, etc.) | [integrations/local-llms/](integrations/local-llms/) | Privacy-first option |

Each guide explains:
- How to load jeffjenx.ai INDEX
- How to invoke agents
- How to load memory files (global + project + namespace)
- How to query jeffjenx.db

**[Integration setup →](integrations/)**

---

## 📋 Projects (Data Sources)

**Your business projects that own data:**

| Project | Role | Data | Repo |
|---------|------|------|------|
| **jeffjenx.com** | Commercial shop | Products, Collections | [../../jeffjenx.com/](../../jeffjenx.com/) |
| **jeffjenx.design** | Design system | Tokens, Components, Icons | [../../jeffjenx.design/](../../jeffjenx.design/) |
| **jeffjenx.net** | Social hub | Links to profiles | [../../jeffjenx.net/](../../jeffjenx.net/) |
| **jeffjenx.me** | Personal portfolio | Resume, Career, Skills | TBD |
| **jeffjenx.info** | Personal inventory | Games, Assets, Collections | TBD |
| **jeffjenx.db** | Data warehouse | Queries across projects | [../jeffjenx.db/](../jeffjenx.db/) |
| **jeffjenx.org** | Organization | Guidelines, Decisions, Memory | [../jeffjenx.org/](../jeffjenx.org/) |

---

## 🛠️ Tools & Utilities

**CLI and UI tools for managing the system:**

- **[tools/init-project.sh](tools/init-project.sh)** — Set up a project to use jeffjenx.ai
- **[tools/log-decision.sh](tools/log-decision.sh)** — Log decisions to memory
- **[tools/validate-agent.sh](tools/validate-agent.sh)** — Validate agent definitions
- **[tools/review-ui/](tools/review-ui/)** — Web UI for approving agent output (MVP in progress)

---

## 🏗️ Architecture

**For developers / system architects:**

### Core Concepts

1. **Agent Specialization** — Each agent has one focused job, restricted tools, clean context
2. **Checkpoints** — Strategic human decision points (story → brief → review)
3. **Data Federation** — Projects own data; queries aggregate without duplication
4. **Progressive Autonomy** — Quality-based confidence tiers; agents graduate based on measured success
5. **Memory-Driven** — Mistakes become rules; rules prevent future mistakes

### File Organization

```
jeffjenx.ai/
├── README.md                         # Project overview
├── INDEX.md                          # This file
├── namespaces/                       # Agent definitions
│   ├── router.config.yaml            # Central routing config
│   └── {agent-name}/
│       ├── agent.md                  # Agent definition + tools
│       ├── jeffjenx.md               # Agent-specific rules
│       └── tools.json                # Tool specifications
├── integrations/                     # Platform adapters
├── governance/                       # Autonomy framework + metrics
└── tools/                            # CLI utilities + review UI
```

### Adding a New Agent

1. Create folder: `namespaces/{agent-name}/`
2. Define agent: `namespaces/{agent-name}/agent.md` (copy from template)
3. Add rules: `namespaces/{agent-name}/jeffjenx.md`
4. Update router: Add entry to `namespaces/router.config.yaml`
5. Create integration guides (if new platform)

**[Agent creation guide →](ARCHITECTURE.md)** *(in progress)*

---

## 📚 Knowledge Base

**Learning resources:**

- **[Software Factory Pattern](https://x.com/sairahul1/status/2058832033628241931)** — Inspiration for this architecture
- **Autonomy Framework** → [governance/autonomy-framework.md](governance/autonomy-framework.md)
- **Data Federation** → [../jeffjenx.db/README.md](../jeffjenx.db/README.md)
- **Organizational Memory** → [../jeffjenx.org/README.md](../jeffjenx.org/README.md)

---

## ❓ FAQ

**Q: Where does my data live?**  
A: In your project repos (jeffjenx.com, .info, .me, etc.). jeffjenx.db just queries across them.

**Q: How do I add custom rules?**  
A: Add rules to the appropriate jeffjenx.md file (global, project, or namespace). Next agent run loads them.

**Q: Can I skip checkpoints?**  
A: Only if the agent has earned the autonomy tier. Tier 0 requires all 3; Tier 3 requires none.

**Q: What if an agent makes a mistake?**  
A: Add a rule to CLAUDE.md to prevent it next time. This is how the system learns.

**Q: How do I know which agent to use?**  
A: Look at your task type in the [Available Agents](#available-agents) section above.

---

## 🚀 Current Status

**Phase 1: Foundation** ✅ In Progress
- Core documentation: ✅
- Agent definitions: ⏳ In progress
- Integration guides: ⏳ Pending
- Memory system: ⏳ Pending

**Phase 2: Implementation** (Weeks 3–5)
- Namespace-specific rules: ⏳ Pending
- Integration guides (all platforms): ⏳ Pending
- Sync mechanism: ⏳ Pending
- MVP review UI: ⏳ Pending

**Phase 3: Pilot** (Weeks 5–7)
- Run real tasks through agent workflow: ⏳ Pending
- Measure confidence metrics: ⏳ Pending
- Refine based on pilot feedback: ⏳ Pending

---

## 📞 Support

Have questions? Check:
1. This INDEX (you're reading it)
2. [governance/autonomy-framework.md](governance/autonomy-framework.md) (how autonomy works)
3. [namespaces/router.config.yaml](namespaces/router.config.yaml) (agent definitions)
4. [../jeffjenx.org/](../jeffjenx.org/) (business rules and decisions)

---

**Last Updated**: 2026-05-29  
**Maintained by**: Your AI Factory System  
**Next Review**: After Phase 1 completion
