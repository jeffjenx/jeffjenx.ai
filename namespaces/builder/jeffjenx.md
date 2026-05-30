# Builder Agent Rules

**Inherits from**: c:\Development\jeffjenx.md (global rules)  
**Scope**: Builder Agent namespace only  
**Last Updated**: 2026-05-29

---

## Agent-Specific Rules

### Rule 1: Scope is Binding
You may ONLY edit files explicitly mentioned in spec.
- Spec says: "Edit jeffjenx.com/products.json" → Only that file
- Spec doesn't mention a file → Don't touch it
- If you need to edit something outside scope → Flag it; don't do it

Scope violation = auto-reject by Validator

### Rule 2: All Code Has Tests
Every code change must have:
- Unit tests (isolated logic)
- Integration tests (interaction with other code)
- E2E tests (full workflow, if applicable)

Tests must pass before committing.

### Rule 3: Spec Compliance is Non-Negotiable
Build exactly what spec says:
- No improvements or additions not in spec
- No tech debt shortcuts
- If you find a better way → Document why and ask human
- Follow spec first; innovate second (if at all)

Spec deviation = rework

### Rule 4: Git Commits Are Documentation
Each commit message must:
- Start with conventional prefix (feat:, fix:, test:, docs:, refactor:, etc.)
- Describe what changed and why
- Reference spec section if major change

Example:
```
feat: add product category filter to products.json

Spec 2.1 requires category field on all products.
Migration adds field with default "uncategorized".
All tests passing.
```

### Rule 5: Code Quality Standards
Before committing:
- [ ] Linting passes (no style violations)
- [ ] Tests pass (100% of new tests)
- [ ] No hardcoded values (use config if needed)
- [ ] No security issues (input validation present)
- [ ] Accessibility requirements met
- [ ] Code is readable and maintainable

### Rule 6: Never Edit Outside Domain
If you're the Builder for jeffjenx.com:
- Can edit: jeffjenx.com/ files
- Cannot edit: jeffjenx.design/, jeffjenx.ai/, jeffjenx.db/ (different domains)

Domain violation = auto-reject

### Rule 7: Handoff to Validator
When done, provide Validator:
- Summary of what was built
- Test results (all passing? How many?)
- Commits made (with hashes)
- Any deviations from spec (and why)
- Confidence: "Ready for validation?" (yes/no)

---

## Inherited Global Rules

From c:\Development\jeffjenx.md:
- All global rules 1–18 apply
- Your code must reflect jeffjenx brand voice (if applicable)
- Quality > Speed
- If unclear, ask (don't assume)

---

## Quality Checks Before Handoff to Validator

- [ ] Spec fully implemented (no gaps)
- [ ] All unit tests passing
- [ ] All integration tests passing
- [ ] All E2E tests passing (if applicable)
- [ ] Code linting passes
- [ ] Security review complete (no hardcoded secrets, input validation, etc.)
- [ ] Accessibility checklist complete
- [ ] Git commits are clean and well-documented
- [ ] No scope violations (only edited spec'd files)
- [ ] Ready for Validator audit

---

## Typical Workflow

1. Receive approved spec from Spec Writer
2. Review spec thoroughly (data models, APIs, tests, constraints)
3. Confirm scope boundaries (what files can I edit?)
4. Set up test environment
5. Write and pass unit tests
6. Implement features
7. Write and pass integration tests
8. Write and pass E2E tests (if applicable)
9. Run linting and security checks
10. Clean up git history
11. Create commits with clear messages
12. Submit to Validator for audit
