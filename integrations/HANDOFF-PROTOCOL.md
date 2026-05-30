# Agent Handoff Protocol

**Date**: May 29, 2026  
**Scope**: How agents communicate and pass work to each other  
**Status**: REFERENCE for Phase 2+ implementation

---

## Overview

Agents hand off work through structured documents. This ensures clarity, traceability, and prevents miscommunication.

```
Researcher → Story Writer → Spec Writer → Builder → Validator → Human
   (file)       (file)        (file)      (file)      (report)   (decision)
```

Each arrow represents a structured handoff with specific format.

---

## Handoff 1: Researcher → Story Writer

**What**: File mapping, patterns, risks  
**Format**: MARKDOWN  
**Filename**: `findings-{project-name}.md`  
**Location**: Shared repo or jeffjenx.org/working/

### Structure

```markdown
# Researcher Findings: {Project Name}

**Date**: 2026-05-29  
**Researcher**: [Agent/Person]  
**Project**: {project-name}  
**Scope**: {brief scope description}

---

## File Map

Directory structure with purpose of each file:

```
{project}/
├── products.json          # Product catalog (23 items, 5 categories)
├── collections.json       # Product collections (4 collections)
├── README.md              # Project documentation
├── scripts/
│   └── sync.js            # Data sync script (not currently working)
└── docs/
    └── product-schema.md  # Schema documentation
```

## Patterns Identified

### Naming Convention
- Files use lowercase-with-hyphens (products.json, collections.json)
- Directories use lowercase (scripts, docs)
- Consistent throughout

### Data Structure
- Products and collections are separate (not linked in any way)
- No inventory or stock tracking
- No user/order data

### Architecture Pattern
- Static JSON files (no database)
- Manual sync script (not running automatically)
- No validation layer

## Risks Flagged

### Critical
- Sync script error: Line 45 in scripts/sync.js has hardcoded path (security risk)
- No data validation: Invalid JSON in products.json would break entire site

### High
- No backup strategy documented
- No versioning for data changes
- Products and collections not linked (inconsistency risk)

### Medium
- README is outdated (last update 2025-08)
- Schema documentation not auto-generated (drift risk)

## Dependencies

### External
- None identified

### Internal
- Data changes in products.json directly impact website
- Sync script depends on network access to external API

## Recommendations for Story Writer

1. **Clarify data relationship**: Should products and collections be linked?
2. **Understand constraints**: Any performance or scale limits?
3. **Confirm ownership**: Who maintains this data? (affects change frequency)
4. **Address risks**: Should stories address sync script error?
```

### Acceptance Criteria for This Handoff

- [x] File map is complete (no missing files)
- [x] Patterns are documented
- [x] Risks are classified by severity
- [x] Dependencies are clear
- [x] Recommendations are actionable
- [x] Story Writer can proceed confidently

---

## Handoff 2: Story Writer → Spec Writer

**What**: Approved user story with acceptance criteria  
**Format**: MARKDOWN  
**Filename**: `story-{story-id}.md`  
**Approval**: ✅ Human approved before handoff  
**Location**: Shared repo or jeffjenx.org/working/

### Structure

```markdown
# User Story {story-id}: {Brief Title}

**Date Created**: 2026-05-29  
**Story Writer**: [Agent/Person]  
**Status**: ✅ APPROVED by human on 2026-05-29  
**Researcher Findings**: findings-jeffjenx-com.md (reference)

---

## User Story

**As a** content manager,  
**I want** to add a new product category filter to the shop,  
**so that** customers can browse products by category.

## Acceptance Criteria

1. [ ] Filter displays all 5 product categories
2. [ ] Clicking a category shows only products in that category
3. [ ] "All Products" option shows all products
4. [ ] Filter persists in URL (bookmarkable)
5. [ ] Mobile layout supports filter (responsive)
6. [ ] Filter loads in < 100ms (performance)

## Failure Paths (What Can Go Wrong)

1. **No products in category**: Display "No products in this category" message
2. **Invalid category in URL**: Redirect to "All Products"
3. **Category renamed**: Old URL links should still work (redirect)
4. **New product added**: Category should auto-appear if not yet in UI

## Edge Cases

1. Product with multiple categories: Should appear in each category view
2. Category with 0 products: Should still display in filter list
3. Very long category names: Truncate gracefully
4. Concurrent data changes: Filter should reflect most recent data

## Business Rules

1. Categories come from products.json (source of truth)
2. No manual category management (derived from data)
3. All categories must be visible (no hidden categories)
4. Categories are case-insensitive for matching

## Dependencies

- None identified (self-contained feature)

## Open Questions (None - Human Approved)

All questions resolved with human before approval.

---

## Notes for Spec Writer

- This is a self-contained feature (no breaking changes to existing code)
- Categories are derived from products.json (see Researcher findings)
- Performance is critical (< 100ms on page load)
```

### Acceptance Criteria for This Handoff

- [x] Story follows "As a / I want / so that" format
- [x] 3–5 acceptance criteria (all testable)
- [x] Edge cases documented
- [x] Failure paths identified
- [x] Business rules explicit
- [x] No open questions (human approved)
- [x] Human approval documented
- [x] Spec Writer can proceed confidently

---

## Handoff 3: Spec Writer → Builder

**What**: Technical specification with data models, APIs, tests  
**Format**: MARKDOWN  
**Filename**: `spec-{story-id}.md`  
**Approval**: ✅ Human approved before handoff  
**Location**: Shared repo or jeffjenx.org/working/

### Structure

```markdown
# Technical Specification {story-id}: Product Category Filter

**Date Created**: 2026-05-29  
**Spec Writer**: [Agent/Person]  
**Status**: ✅ APPROVED by human on 2026-05-29  
**Story Reference**: story-{story-id}.md  

---

## Technical Overview

Add client-side filtering UI to display products by category. Categories derived from products.json data. No backend changes required.

## Data Model Changes

**None**. Using existing products.json structure.

## UI Specifications

### Filter Component

```html
<div class="filter-categories">
  <label>Category Filter:</label>
  <select id="category-filter" onchange="filterProducts(this.value)">
    <option value="">All Products</option>
    <option value="web">Web</option>
    <option value="warez">Warez</option>
    <option value="gb">Game Boy</option>
    <option value="nes">NES</option>
    <option value="shop">Shop</option>
  </select>
</div>

<div id="products-list">
  <!-- Products rendered here -->
</div>
```

### Styling

Use existing CSS classes from jeffjenx.design. Filter should inherit color scheme.

## JavaScript Implementation

```javascript
function filterProducts(category) {
  const products = document.querySelectorAll('[data-category]');
  
  if (category === '') {
    // Show all
    products.forEach(p => p.style.display = 'block');
  } else {
    // Filter by category
    products.forEach(p => {
      const productCategory = p.dataset.category;
      p.style.display = productCategory === category ? 'block' : 'none';
    });
  }
  
  // Update URL for bookmarking
  const url = new URL(window.location);
  url.searchParams.set('category', category);
  window.history.replaceState({}, '', url);
  
  // Performance: Should complete in < 100ms
  console.time('filter');
  // ... filtering code ...
  console.timeEnd('filter');
}
```

## Tests Required

### Unit Tests
- [ ] filterProducts() with valid category returns correct products
- [ ] filterProducts('') returns all products
- [ ] filterProducts('invalid') returns nothing (gracefully)
- [ ] URL updates correctly with selected category

### E2E Tests
- [ ] Load page → see all products
- [ ] Select category → only that category shows
- [ ] Select "All" → see all products
- [ ] Refresh with category in URL → correct category selected
- [ ] Mobile viewport → filter is usable

### Performance Tests
- [ ] filterProducts() completes in < 100ms for 50+ products
- [ ] No layout thrashing or reflows

## Security & Compliance

- No security concerns (client-side filtering only)
- No data exposure (using public product data)
- No user tracking (session data not modified)

## Accessibility

- [ ] Filter label associated with select
- [ ] Keyboard navigable (arrow keys, Enter)
- [ ] Screen reader announces filter and available options
- [ ] Color contrast for selected state (WCAG AA)

## Scope

**Can Edit**:
- jeffjenx.com/scripts/filter.js (new file)
- jeffjenx.com/index.html (add filter HTML)
- jeffjenx.com/styles.css (add filter CSS)

**Cannot Edit**:
- products.json (data only)
- Any other projects

## Implementation Notes

- Filter is cosmetic (doesn't affect actual product display system)
- Uses existing data (no API calls needed)
- Client-side only (no backend changes)
- Gracefully degradable (works without JavaScript; just no filtering)

---

## Test Results (After Builder Completes)

All tests must pass before handoff to Validator:

- [ ] Unit tests: [count] passed, 0 failed
- [ ] E2E tests: [count] passed, 0 failed
- [ ] Performance: All < 100ms
- [ ] Accessibility: WCAG AA compliant
```

### Acceptance Criteria for This Handoff

- [x] Spec is implementable (no ambiguity)
- [x] Data models specified (or "no changes")
- [x] UI/APIs fully defined
- [x] Test strategy documented (unit/E2E/perf)
- [x] Security & accessibility addressed
- [x] Scope is clear (what Builder CAN edit)
- [x] No infrastructure invented
- [x] Human approval documented
- [x] Builder can proceed confidently

---

## Handoff 4: Builder → Validator

**What**: Completed implementation with test results  
**Format**: TEXT/JSON (test output) + GIT COMMITS  
**Filename**: `implementation-{story-id}.json` or git log  
**Location**: Committed to project repo

### Structure

```json
{
  "story_id": "story-001",
  "date_completed": "2026-05-29T14:30:00Z",
  "builder": "Builder Agent",
  "implementation_summary": {
    "files_created": ["scripts/filter.js"],
    "files_modified": ["index.html", "styles.css"],
    "files_deleted": []
  },
  "test_results": {
    "unit_tests": {
      "total": 8,
      "passed": 8,
      "failed": 0
    },
    "e2e_tests": {
      "total": 5,
      "passed": 5,
      "failed": 0
    },
    "performance": {
      "filter_speed_ms": 25,
      "requirement_ms": 100,
      "passed": true
    }
  },
  "code_quality": {
    "linting": "PASS",
    "coverage": "92%",
    "accessibility_audit": "PASS"
  },
  "git_commits": [
    {
      "hash": "abc123def",
      "message": "feat: add product category filter (story-001)"
    }
  ],
  "deviations_from_spec": "None",
  "ready_for_validation": true,
  "validator_notes": "All tests passing. Ready for audit."
}
```

### Acceptance Criteria for This Handoff

- [x] Implementation matches spec exactly
- [x] All acceptance criteria met (testable)
- [x] All tests passing
- [x] Code quality checks pass (linting, coverage, accessibility)
- [x] Git history is clean (clear commits)
- [x] No deviations from spec (or documented)
- [x] Validator can audit with confidence

---

## Handoff 5: Validator → Human

**What**: Audit report with findings and recommendation  
**Format**: MARKDOWN  
**Filename**: `audit-report-{story-id}.md`  
**Location**: jeffjenx.org/memory/audit-log.md (entry) or shared repo

### Structure

```markdown
# Audit Report {story-id}: Product Category Filter

**Date Audited**: 2026-05-29  
**Validator**: Validator Agent  
**Implementation**: Completed by Builder Agent  
**Status**: ✅ PASS

---

## Acceptance Criteria Audit

- [x] AC1: Filter displays all 5 categories — PASS
- [x] AC2: Clicking category shows only that category — PASS
- [x] AC3: "All Products" option shows all — PASS
- [x] AC4: Filter persists in URL — PASS
- [x] AC5: Mobile layout responsive — PASS
- [x] AC6: Filter loads in < 100ms — PASS (actual: 25ms)

## Spec Compliance

- [x] UI matches specification — PASS
- [x] JavaScript implementation correct — PASS
- [x] CSS styling applied — PASS
- [x] Test coverage meets requirement — PASS (92% > 80%)

## Code Quality

- [x] Linting — PASS (0 violations)
- [x] Test coverage — 92% (exceeds 80% requirement)
- [x] No hardcoded values — PASS
- [x] No security concerns — PASS

## Brand Consistency

- [x] JavaScript follows existing patterns — PASS
- [x] Component naming consistent — PASS
- [x] CSS uses design tokens — PASS
- [x] Tone/messaging (if any) — N/A

## Accessibility

- [x] Color contrast — WCAG AA compliant
- [x] Keyboard navigation — PASS
- [x] Screen reader labels — PASS
- [x] Mobile usability — PASS

## Performance

- [x] Filter responds in < 100ms — PASS (actual: 25ms)
- [x] No layout thrashing — PASS
- [x] Memory usage acceptable — PASS

## Git & Commits

- [x] Commits are clean and descriptive — PASS
- [x] Commit messages follow convention — PASS
- [x] History is linear (no merge conflicts) — PASS

## Issues Found

**Critical**: None  
**High**: None  
**Medium**: None  
**Low**: None

## Confidence Score

```
accuracy = (6/6 criteria met) / 6 = 100%
consistency = 100% (pattern matches past decisions)
override_rate = 0% (no rejections)

confidence = (100% × 0.5) + (100% × 0.3) + (0% × 0.2) = 100%
Result: 100% (Tier 2 - Expert)
```

## Recommendation

✅ **PASS - Ready to Merge/Deploy**

All acceptance criteria met. All tests passing. No issues found. Code quality excellent. Recommend proceeding.

---

## Findings Logged To

- jeffjenx.org/memory/audit-log.md (confidence score and metrics)
- jeffjenx.org/memory/DOCUMENTATION-MAINTENANCE-LOG.md (work log)
```

### Acceptance Criteria for This Handoff

- [x] All acceptance criteria audited
- [x] Spec compliance verified
- [x] Code quality confirmed
- [x] Brand consistency checked
- [x] Accessibility validated
- [x] Confidence score calculated
- [x] Recommendation is clear
- [x] Human can make final decision

---

## Summary: Handoff Protocol

| From | To | What | Format | Approval |
|------|---|----|--------|----------|
| Researcher | Story Writer | Findings (file map, patterns, risks) | MD | Agent delivers; no gate |
| Story Writer | Spec Writer | Approved story + acceptance criteria | MD | ✅ Human approves |
| Spec Writer | Builder | Technical spec with data models, tests | MD | ✅ Human approves |
| Builder | Validator | Implementation + test results | JSON/git | No gate; Validator audits |
| Validator | Human | Audit report with recommendation | MD | No gate; human decides |

**Key Points**:
- Each handoff is a specific document with clear structure
- Approval gates (human approval) happen between Story Writer and Builder only
- Validator never approves; only reports; human decides
- All files are traceable and logged in memory
