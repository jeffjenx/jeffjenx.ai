# Autonomy Framework — Confidence Tiers & Human Checkpoints

This document defines how jeffjenx.ai agents progress from supervised to autonomous operation. The system is **quality-driven** and **user-controlled**: agents advance tiers based on measured success, not speed.

## Core Philosophy

- **Accuracy > Speed**: Quality metrics drive autonomy, not velocity
- **Measured Progress**: Every decision tracked; confidence quantified
- **Strategic Checkpoints**: Humans remain in loop where judgment matters
- **Learning System**: Mistakes become rules; rules prevent future mistakes

## Confidence Tiers

### Tier 0: Learning (Maximum Oversight)

**When to use**: New agents, unfamiliar domains, high-risk tasks

**Checkpoints Required**: 3 (Story → Brief → Deploy)

| Checkpoint | Decision | Gate |
|------------|----------|------|
| #1: Story Approval | "Did we understand the requirement?" | ✓ Human approves user story and acceptance criteria |
| #2: Brief Approval | "Is the technical approach sound?" | ✓ Human approves technical specification |
| #3: Validator Review | "Does implementation match specs?" | ✓ Human reviews Validator report + output in UI |

**Example flow**:
```
1. Researcher Agent maps codebase (read-only)
2. Story Agent generates user story
3. ⏸ CHECKPOINT #1: You read story; approve or revise
4. Spec Agent generates technical brief
5. ⏸ CHECKPOINT #2: You read brief; approve or revise
6. Builder Agent executes task
7. Validator Agent audits output
8. ⏸ CHECKPOINT #3: You review in UI; approve or revise
9. Deploy/commit
```

**Success Criteria for Tier 0**:
- 0 rejections in 3 consecutive runs, OR
- 5 consecutive approvals with no requested revisions, OR
- Human explicitly says "This is ready for Tier 1"

### Tier 1: Trusted (Moderate Oversight)

**When to use**: Proven agents in familiar domains, moderate-risk tasks

**Checkpoints Required**: 2 (Brief → Deploy)

| Checkpoint | Decision | Gate |
|------------|----------|------|
| #1: Brief Approval | "Is the technical approach sound?" | ✓ Human approves technical specification |
| #2: Validator Review | "Does implementation match specs?" | ✓ Human reviews Validator report + output |

**Example flow**:
```
1-2. Researcher + Story agents run without checkpoints
3. ⏸ CHECKPOINT #1: Brief approval (skipped story; we know the domain)
4-7. Spec + Builder agents run
8. ⏸ CHECKPOINT #2: Validator review
```

**Success Criteria for Tier 1**:
- 5 consecutive successful runs (Validator says "clean"), OR
- Task complexity low enough (defined per domain), OR
- Human says "Tier 1 is fine for this task type"

### Tier 2: Expert (Minimal Oversight)

**When to use**: High-confidence agents, routine tasks, low-risk domains

**Checkpoints Required**: 1 (Deploy Only)

| Checkpoint | Decision | Gate |
|------------|----------|------|
| #1: Validator Review | "Does implementation match specs?" | ✓ Brief review; Validator says "clean" → auto-approve, OR flag for review |

**Example flow**:
```
1-5. All agents run, Brief review happens async
6-8. Validator runs; reports to async queue
7. Validator says "clean" → auto-approved
   OR Validator flags issues → human decides
```

**Success Criteria for Tier 2**:
- 10+ consecutive successful runs (no manual overrides), OR
- Task is routine/mechanical (< 1 hour human review time for Tier 1), OR
- Override rate < 5%

### Tier 3: Autonomous (No Scheduled Oversight)

**When to use**: Highly specialized agents, proven track records, predictable outcomes

**Checkpoints Required**: 0 (Async Validation Only)

All agents run → Validator audits asynchronously → results logged to jeffjenx.org/memory/audit-log.md → human review optional/periodic.

**Example flow**:
```
1-8. All agents run; no human intervention scheduled
9. Validator runs in background; results logged
10. Human reviews audit log periodically (daily/weekly)
    OR reviews specific findings if flagged as "critical"
```

**Success Criteria for Tier 3**:
- 20+ consecutive successful runs, OR
- Domain is proven stable (no mistakes in 4+ weeks), OR
- Override rate < 2%, OR
- Human explicitly says "This is autonomous"

## Scoring & Metrics

### Confidence Score Calculation

```
confidence_score = (accuracy * 0.5) + (consistency * 0.3) + (override_rate * 0.2)

Where:
  accuracy = (total_runs - errors) / total_runs
  consistency = (runs_matching_past_decisions / total_runs)
  override_rate = (runs_where_human_rejected / total_runs) [lower is better]
```

**Formula**:
- Score 0-50%: Tier 0 (Learning)
- Score 50-75%: Tier 1 (Trusted)
- Score 75-90%: Tier 2 (Expert)
- Score 90-100%: Tier 3 (Autonomous)

### Metrics to Track (Per Agent, Per Task Type)

1. **Accuracy**: Did output match acceptance criteria? (0-100%)
2. **Override Rate**: How often did human reject/revise? (0-100%)
3. **Consistency**: How often did output match past similar decisions? (0-100%)
4. **Time-to-Approval**: How long from completion to human approval? (minutes)
5. **Correction Rate**: How many corrections needed per 10 tasks? (0-100%)
6. **Validation Findings**: How many issues did Validator catch per run? (count)

### Logging Format (in jeffjenx.org/memory/audit-log.md)

```markdown
## Task ID: copywriting-001
- **Date**: 2026-05-29
- **Agent**: Copywriting Agent
- **Task**: Generate product description for 3 items
- **Tier**: 1 (Trusted)
- **Status**: Approved
- **Accuracy**: 100% (all acceptance criteria met)
- **Override Rate**: 0/1 (approved first time)
- **Consistency**: 100% (matches past style decisions)
- **Validator Findings**: 0 critical, 0 important, 1 minor (tone suggestion)
- **Confidence Score**: 92% (↑ trending toward Tier 2)
- **Notes**: Excellent consistency with brand guidelines. Validator found only stylistic suggestions.
```

## Tier Progression Rules

### Automatic Advancement

An agent/task type automatically advances tiers when:

1. **Tier 0 → Tier 1**: 5 consecutive successful runs (100% approved, 0 corrections)
2. **Tier 1 → Tier 2**: 10 consecutive successful runs (confidence > 80%, override rate < 5%)
3. **Tier 2 → Tier 3**: 20 consecutive successful runs (confidence > 90%, zero critical Validator findings)

### Manual Adjustment

You can manually adjust tiers:
- Promote: "This task is simple enough for Tier 2" (skip the runs)
- Demote: "Too many mistakes lately; move back to Tier 1" (when confidence drops)
- Freeze: "Don't let this task advance automatically; review manually"

### Regression (Demotion Rules)

An agent automatically drops tiers if:
- **Tier 3 → Tier 2**: Accuracy < 85% or critical Validator findings (2+) in last 5 runs
- **Tier 2 → Tier 1**: Accuracy < 75% or override rate > 10%
- **Tier 1 → Tier 0**: Accuracy < 60% or override rate > 20%

## Task-Type Baseline Tiers

Different task types start at different tiers based on risk and predictability.

| Task Type | Starting Tier | Rationale |
|-----------|---------------|-----------|
| Product copywriting | 0 | Brand consistency critical; high-risk |
| Inventory sync | 1 | Mechanical; low-risk |
| Data validation | 0 | Accuracy critical |
| Report generation | 1 | Predictable; low stakes |
| Bug fixes | 0 | High technical risk |
| Routine updates | 2 | Well-defined; low complexity |
| System monitoring | 1 | Important but predictable |

(Customize per your projects)

## Integration with CLAUDE.md

Every time a Validator finds an issue, ask:

**"Would a rule in CLAUDE.md have prevented this?"**

- **Yes** → Add rule to relevant jeffjenx.md file (global, project, or namespace)
- **Next run** → Agent loads the rule; issue prevented

Example:
```
❌ Issue Found: Agent generated "utilize" instead of "use" (brand voice violation)
✓ Add to namespace jeffjenx.md: "Use common words (use, not utilize; help, not assist)"
```

## Checkpoints in Practice

### Checkpoint #1: Story Review

**You see**:
- User story: "As a customer, I want to..."
- Acceptance criteria: "Given X, when Y, then Z"
- Failure paths: "If payment fails..."
- Edge cases: "Multi-tenant concern: tenant A shouldn't see tenant B's data"

**You decide**:
- ✓ Approve (proceed to brief)
- ✗ Reject (back to Researcher; gather more info)
- ? Ask questions (agent clarifies, you re-review)

### Checkpoint #2: Brief Review

**You see**:
- Data models: "Add payment_status field to Invoice"
- API changes: "POST /invoices/{id}/send-reminder"
- Frontend changes: "Add 'Send Reminder' button to invoice card"
- Tests required: "Test that tenant A can't remind tenant B's invoices"

**You decide**:
- ✓ Approve (proceed to build)
- ✗ Reject (catch architectural issues now, not after 10 files)
- ? Clarify (e.g., "Should we use Resend or Twilio for SMS?")

### Checkpoint #3: Validator Review (in UI)

**You see**:
- Output artifact(s) (code, copy, design, etc.)
- Validator report: ✓ All criteria met, ⚠ Minor issues, ✗ Critical gaps
- Brand consistency checklist: ✓ Tone matches, ✓ Vocabulary correct
- Data sources used: "Pulled from jeffjenx.com/products.json"

**You decide**:
- ✓ Approve (deploy)
- ✗ Reject (back to builder; address Validator findings)
- ? Revise inline (quick fix; re-validate)

## Dashboard (Future)

Future versions will show:

```
┌─────────────────────────────────────────────────────────┐
│ Agent Autonomy Dashboard                                │
├─────────────────────────────────────────────────────────┤
│                                                           │
│ Copywriting Agent                                       │
│ Tier: 1 (Trusted)  [progress bar: 60% to Tier 2]       │
│ Accuracy: 92%  | Override Rate: 8%  | Confidence: 84% │
│ Last 5 runs: ✓ ✓ ✓ ? ✓  (? = minor issue)              │
│ Next milestone: 5 more runs for Tier 2                 │
│                                                           │
│ Inventory Sync Agent                                    │
│ Tier: 2 (Expert)  [progress bar: 85% to Tier 3]        │
│ Accuracy: 98%  | Override Rate: 2%  | Confidence: 91% │
│ Last 5 runs: ✓ ✓ ✓ ✓ ✓  (clean sweep)                 │
│ Next milestone: 15 more runs for Tier 3                │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

## Current Status

**Phase 1: Foundation** (Complete)
- Tier definitions: ✅
- Scoring model: ✅
- Checkpoint protocols: ✅
- Progression rules: ✅

**Phase 2: Implementation** (In Progress)
- Audit log structure: In progress
- Dashboard mockup: Pending
- Metrics collection: Pending

## Next Steps

1. Review and customize baselines for your use cases
2. Set up audit logging in jeffjenx.org/memory/audit-log.md
3. Start Phase 1 pilots; collect metrics
4. After 5-10 runs, review progression rules
5. Adjust confidence thresholds based on observed performance
