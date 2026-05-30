# Error Handling Workflow

**Date**: May 29, 2026  
**Scope**: How jeffjenx.ai agents handle errors and failures  
**Status**: REFERENCE for Phase 2+ implementation

---

## Philosophy

**Principle**: Fail gracefully, log comprehensively, escalate strategically.

- **Graceful Degradation**: Use defaults and fallbacks when possible
- **Comprehensive Logging**: Every error is logged to audit-log.md or blockers-and-fixes.md
- **Strategic Escalation**: Critical errors go to human immediately; minor issues are logged for review

---

## Error Categories

### Category 1: Configuration Errors (Load-Time)

**Scenario**: Agent tries to load jeffjenx.md but file is missing or malformed

**Handling Strategy**: **Graceful Degradation**

```
IF global config missing:
  → Log warning: "Global config not found; using defaults"
  → Load hardcoded defaults (permissive baseline)
  → Continue with warning in audit log

IF namespace config missing:
  → Log info: "Namespace config not found; using global + project"
  → Continue with just global (or project if exists)
  → This is OK; namespace is optional

IF config malformed (invalid YAML/JSON):
  → Log error: "Config file syntax error at [line:column]"
  → Do NOT proceed
  → Alert human: "Config load failed; manual intervention needed"
  → Escalate to human (stop execution)
```

**Action**: Log to `jeffjenx.org/memory/blockers-and-fixes.md` with severity "Critical"

---

### Category 2: Tool Failures (Execution-Time)

**Scenario**: Agent tries to use tool (e.g., read, grep, edit) but it fails

**Handling Strategy**: **Fail-Fast with Clear Error Message**

```
IF read fails (file not found, permission denied):
  → Stop current task
  → Log error: "Cannot read [file]; reason: [permission/not found/etc]"
  → Report to human: "Task blocked: cannot access required file"
  → Escalate to human

IF grep finds no results (legitimate case):
  → Log info: "Pattern not found in codebase (expected for some searches)"
  → Return empty results
  → Continue (not an error)

IF edit fails (no write permission, file locked):
  → Stop current task
  → Log error: "Cannot edit [file]; reason: [permission/locked/etc]"
  → Report to human: "Task blocked: insufficient permissions"
  → Escalate to human
```

**Action**: Log to `jeffjenx.org/memory/blockers-and-fixes.md` with severity "High"

---

### Category 3: Validation Failures (Rule Violations)

**Scenario**: Agent output violates SAMPLES.md rules or acceptance criteria

**Handling Strategy**: **Report and Escalate**

```
IF output violates brand voice rule:
  → Log violation: "Output uses 'synergy' (prohibited per SAMPLES.md)"
  → Report to human: "Output violates brand rule; please revise"
  → Request Validator re-audit after Builder revises
  → DO NOT auto-approve

IF output fails acceptance criteria:
  → Log failure: "Acceptance criteria #3 not met: expected [X], got [Y]"
  → Report to human: "Task incomplete; fails acceptance test"
  → Request Builder re-implement
  → DO NOT auto-approve

IF Validator detects inconsistency with past decisions:
  → Log inconsistency: "Output contradicts Rule #7 from 2026-05-15"
  → Report to human: "Possible inconsistency detected"
  → Flag for review but DO NOT auto-block
```

**Action**: Log to `jeffjenx.org/memory/audit-log.md` and `blockers-and-fixes.md`

---

### Category 4: Agent Logic Errors (Implementation Bugs)

**Scenario**: Agent code has a bug (edge case, logic error, etc.)

**Handling Strategy**: **Log, Report, and Update Rules**

```
IF agent produces unexpected output:
  → Validator catches it during audit
  → Log error with full context: input, output, expected, actual
  → Report to human with confidence score impact
  → Escalate if severity is Critical

IF error is repeatable (same input causes same error):
  → Log pattern: "Error pattern detected; occurs [frequency]"
  → Create rule update: "When [condition], agent should [correction]"
  → Add to memory/blockers-and-fixes.md as "Recurring Issue"
  → Human reviews and decides: fix agent code or update rule?

IF error is one-off (edge case, user input):
  → Log as "One-off error; may be user-specific"
  → Continue; monitor for repeat
```

**Action**: Log to `jeffjenx.org/memory/blockers-and-fixes.md` with pattern analysis

---

### Category 5: Human/Process Errors (Human Mistake)

**Scenario**: Human provides invalid input or unclear requirements

**Handling Strategy**: **Ask for Clarification**

```
IF requirement is ambiguous:
  → Agent lists "Open Questions" in output
  → DO NOT proceed without clarification
  → Request human to clarify

IF human provides conflicting requirements:
  → Agent identifies conflict: "Rule #2 conflicts with requirement"
  → Report conflict to human
  → Request human to choose path forward
  → DO NOT guess

IF input is invalid (e.g., project path doesn't exist):
  → Agent reports: "Project path [X] does not exist"
  → Request human to verify and retry
  → DO NOT proceed
```

**Action**: Log to `jeffjenx.org/memory/blockers-and-fixes.md` as "Input Validation Error"

---

## Recovery Strategies by Severity

### Severity: CRITICAL (Stop Immediately)

Examples:
- Config file missing or malformed
- Cannot access required data
- Security violation detected
- Acceptance criteria cannot be met

**Response**:
1. Log error (critical)
2. Report to human immediately (do not continue)
3. Wait for human decision (fix or override)
4. Create rule to prevent recurrence

**Example**:
```
[Critical Error] Config Load Failure
  Date: 2026-05-29 14:30
  Agent: Story Writer
  Error: jeffsynth.md not found at c:\Development\jeffjenx.md
  Impact: Agent cannot start; no configuration loaded
  Action: Human must verify file exists before retry
  Status: BLOCKED (awaiting human)
```

---

### Severity: HIGH (Log & Report; Continue with Caution)

Examples:
- File permission denied
- Non-critical tool fails
- Validator finds issues

**Response**:
1. Log error (high)
2. Report to human (with details)
3. Request decision: continue or halt?
4. If continue: proceed with limitations

**Example**:
```
[High Error] File Permission Denied
  Date: 2026-05-29 14:35
  Agent: Builder
  File: c:\Development\jeffjenx.com\products.json
  Error: Write permission denied (file owned by system)
  Action: Builder cannot modify; human must grant permission
  Status: BLOCKED (awaiting human action)
```

---

### Severity: MEDIUM (Log & Continue)

Examples:
- Pattern search finds nothing (expected)
- Minor validation warning
- Consistency flag (not violation)

**Response**:
1. Log issue (medium)
2. Continue execution
3. Report at end (in audit summary)
4. Human reviews in audit log

**Example**:
```
[Medium] Consistency Warning
  Date: 2026-05-29 14:40
  Agent: Validator
  Finding: Output uses "I will" (past decisions use "will be")
  Severity: Medium (style inconsistency, not violation)
  Impact: Affects confidence score by 3%
  Status: NOTED (continue; human reviews)
```

---

### Severity: LOW (Log; No Action Required)

Examples:
- Informational log entries
- Performance notes
- Style suggestions

**Response**:
1. Log issue (low)
2. Continue execution
3. Include in audit summary
4. Human can ignore or address

**Example**:
```
[Low] Style Suggestion
  Date: 2026-05-29 14:45
  Agent: Validator
  Suggestion: Consider adding comma after list item (Validator agent is opinionated)
  Impact: No functional impact; aesthetic only
  Status: LOGGED (human can ignore)
```

---

## Logging Format

All errors logged to:
- **c:\Development\jeffjenx.org\memory\audit-log.md** (agent performance metrics)
- **c:\Development\jeffjenx.org\memory\blockers-and-fixes.md** (problems and solutions)

### Audit Log Entry (Severity: All)

```markdown
## Task ID: [task-id]
- **Date**: 2026-05-29 HH:MM
- **Agent**: [Agent Name]
- **Task**: [Brief description]
- **Error Occurred**: Yes/No
- **Error Type**: [Configuration/Tool/Validation/Logic/Human Input]
- **Severity**: Critical/High/Medium/Low
- **Error Message**: [Exact error text]
- **Impact**: [What was blocked or affected]
- **Resolution**: [How was it resolved, or what human needs to do]
- **Action Item**: [If human intervention needed]
```

### Blocker Entry (Severity: Critical, High, Medium)

```markdown
## Blocker ID: [blocker-id]
- **Date Reported**: 2026-05-29
- **Agent**: [Agent Name]
- **Type**: [Configuration/Tool/Validation/Logic/Human Input]
- **Description**: [What's blocked]
- **Severity**: Critical/High/Medium
- **Blocker Until**: [What needs to happen to unblock]
- **Assigned To**: [Human/Agent name]
- **Status**: Open/In Progress/Resolved
- **Resolution**: [How was it solved, or action taken]
```

---

## Prevention Strategies

### For Configuration Errors

✅ **Checksum validation**: Verify config files are not corrupted  
✅ **Defaults as fallback**: Always have sensible defaults if config missing  
✅ **Early validation**: Validate config at agent startup, not mid-execution

### For Tool Failures

✅ **Permission checks**: Verify access before attempting operation  
✅ **Try-catch patterns**: Wrap tool calls in error handling  
✅ **Graceful degradation**: Use alternatives if tool fails (e.g., if grep fails, use read+search)

### For Validation Failures

✅ **Pre-audit checks**: Review against rules BEFORE finalizing  
✅ **Template enforcement**: Use templates to prevent common errors  
✅ **Acceptance test staging**: Run full validation before declaring done

### For Agent Logic Errors

✅ **Comprehensive testing**: Unit + integration + E2E tests before production  
✅ **Rule extraction**: After each error, update rules to prevent recurrence  
✅ **Monitoring & alerting**: Validator tracks error patterns

### For Human Errors

✅ **Clear requirements**: Ask clarifying questions before proceeding  
✅ **Input validation**: Reject invalid inputs; don't guess  
✅ **Escalation paths**: Block ambiguous tasks; don't make assumptions

---

## Escalation Matrix

Who handles which errors:

| Error Type | Detection | First Response | Escalation |
|---|---|---|---|
| Config missing | Agent startup | Use defaults + log | Human (if critical) |
| Permission denied | Tool attempt | Report + block | Human (must fix) |
| Validation failure | Validator | Report findings | Human (review + decide) |
| Logic bug | Validator | Increase severity | Human (if pattern repeats) |
| Ambiguous requirement | Agent processing | Ask question + list assumptions | Human (must clarify) |
| One-off error | Validator | Log + continue | Human (if repeated) |

---

## Example Workflows

### Workflow 1: Configuration Missing (Graceful)

```
Agent startup:
  1. Try to load c:\Development\jeffjenx.md (global)
  2. File not found
  3. Log: "Warning: global config not found"
  4. Load hardcoded defaults (permissive baseline)
  5. Try to load project config (if applicable)
  6. Try to load namespace config
  7. Report: "Proceeding with limited configuration"
  8. Continue (degraded mode)
  → Agent works with defaults; human reviews audit log
```

### Workflow 2: Validation Failure (Report & Escalate)

```
Builder finishes task:
  1. Submits to Validator
  2. Validator audits against acceptance criteria
  3. Validator finds: Acceptance criterion #2 not met
  4. Log: "Critical - AC #2 failed: expected [X], got [Y]"
  5. Report: "Implementation incomplete; 1 critical issue"
  6. Status: ❌ FAIL (send back to Builder)
  7. Builder reviews issue; revises implementation
  8. Re-submit to Validator
  → Validator re-audits; if pass, proceed; if fail, repeat
```

### Workflow 3: Ambiguous Requirement (Ask First)

```
Story Writer starts task:
  1. Receives requirement: "Users should filter products better"
  2. Requirement is vague ("better" is undefined)
  3. Story Writer lists open questions:
     - What does "better" mean? (Faster? More options? Clearer UI?)
     - What categories exist? (From Researcher findings)
     - Should existing filters be updated or new ones added?
  4. Report: "Cannot proceed; requirement ambiguous"
  5. Request human to clarify
  6. Human provides clarity: "Add category filter; keep existing filters"
  7. Story Writer proceeds with clarified requirement
  → Story writing completes successfully
```

---

## Final Principle

**Errors are learning opportunities**, not failures.

- Every error is logged
- Rules are updated to prevent recurrence
- Confidence scores reflect reliability
- System learns and improves over time

Repeat errors trigger:
1. Rule creation
2. Training update (SAMPLES.md or jeffjenx.md)
3. Agent tier adjustment (demotion if pattern repeats)
4. Documentation of workaround or permanent fix

This keeps the system reliable and continuously improving.
