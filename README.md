# Kinetic Code Skills

## Overview

This skill pack combines 20 coding quality skills:

- 10 code functionality skills
- 10 testing skills

The pack is organized so each skill has its own `SKILL.md` file in one of these directories:

- `/code-functionality-skills`
- `/testing-skills`

---

## Included Skills and Purposes

### Code Functionality Skills (located in `/code-functionality-skills`)

1. **Input Validation at All Entry Points**  
	Purpose: Prevent invalid data from entering and corrupting the system.
2. **Explicit Return Values and Exit Conditions**  
	Purpose: Keep behavior predictable by ensuring each path returns explicitly.
3. **Handle All Error Cases Explicitly**  
	Purpose: Avoid silent failures by handling risky operations with clear errors.
4. **Use Defensive Coding for Null/Undefined**  
	Purpose: Prevent null/undefined runtime crashes through guard checks.
5. **Maintain Consistent State Throughout Operations**  
	Purpose: Prevent data corruption with atomic updates and rollback on failure.
6. **Validate Data Types and Contracts**  
	Purpose: Catch contract and type mismatches early.
7. **Use Clear Exit Conditions for Loops**  
	Purpose: Prevent infinite loops, hangs, and recursion overflows.
8. **Verify State Before Performing Actions**  
	Purpose: Ensure destructive or sensitive actions only run in valid states.
9. **Provide Clear Feedback on Operation Results**  
	Purpose: Make outcomes clear for users and operators (success/failure/partial).
10. **Test Expected vs Actual Behavior**  
	 Purpose: Confirm implementation matches specification and expected behavior.

### Testing Skills (located in `/testing-skills`)

1. **Test Coverage Must Include Normal Cases**  
	Purpose: Confirm the core happy path works as expected.
2. **Test Edge Cases and Boundaries**  
	Purpose: Catch boundary and off-by-one defects.
3. **Test Error Cases and Invalid Inputs**  
	Purpose: Verify graceful failure and clear validation behavior.
4. **Verify State Changes Are Correct**  
	Purpose: Confirm intended changes happen and unintended changes do not.
5. **Test Asynchronous Operations Fully**  
	Purpose: Validate async success, rejection, timeout, and concurrency behavior.
6. **Test Integration Between Components**  
	Purpose: Ensure data flow and contracts work across component boundaries.
7. **Use Meaningful Test Names and Descriptions**  
	Purpose: Keep tests self-documenting and maintainable.
8. **Isolate Tests From External Dependencies**  
	Purpose: Keep tests deterministic, fast, and non-flaky.
9. **Verify Both Happy Path and Error Paths**  
	Purpose: Ensure reliability across both success and failure paths.
10. **Maintain Test Coverage and Keep Tests Updated**  
	 Purpose: Preserve long-term trust in tests as code evolves.

---

## Directory Layout

```text
kinetic-coding-rules/
  kinetic-code-skills.md
  code-functionality-skills/
	 <skill-name>/
		SKILL.md
  testing-skills/
	 <skill-name>/
		SKILL.md
```

---

## How These Skills Work Together

```text
CODE QUALITY CYCLE:

1. Write code using functionality skills
2. Verify behavior using testing skills
3. Review implementation against both checklists
4. Ship reliable and maintainable code
```

### Functionality Skills Ensure

- Inputs are validated
- Behavior is explicit and predictable
- Errors are handled with context
- State changes are safe and consistent
- Operation outcomes are clear

### Testing Skills Ensure

- Normal behavior is verified
- Edge and error cases are covered
- State transitions are checked
- Integrations and async paths are validated
- Test suites stay readable, isolated, and current

---

## Quick Reference

| # | Code Functionality Skill | Purpose |
|---|---|---|
| 1 | Input Validation | Prevent invalid data corruption |
| 2 | Explicit Returns | Make behavior predictable |
| 3 | Error Handling | Prevent silent failures |
| 4 | Null/Undefined Checks | Prevent runtime crashes |
| 5 | Consistent State | Prevent partial updates and data loss |
| 6 | Type/Contract Validation | Catch schema and type issues early |
| 7 | Clear Loop Exits | Prevent hangs and infinite loops |
| 8 | State Verification | Prevent invalid operations |
| 9 | Operation Feedback | Provide clear result visibility |
| 10 | Behavior Testing | Validate expected vs actual behavior |

| # | Testing Skill | Purpose |
|---|---|---|
| 1 | Normal Case Coverage | Verify expected usage works |
| 2 | Edge/Boundary Coverage | Catch limit-condition defects |
| 3 | Error/Invalid Input Coverage | Verify graceful failure behavior |
| 4 | State Change Verification | Confirm correct modifications only |
| 5 | Async Testing | Catch promise/timing/concurrency bugs |
| 6 | Integration Testing | Verify component interaction correctness |
| 7 | Meaningful Test Names | Keep tests understandable |
| 8 | Test Isolation | Prevent flaky dependency-driven failures |
| 9 | Happy + Error Path Coverage | Ensure robustness in all outcomes |
| 10 | Coverage Maintenance | Keep tests useful as code changes |

---

## Success Criteria

This skill pack is working as intended when:

- Each of the 20 skills exists as a standalone `SKILL.md`
- Functionality and testing skills are separated into their own directories
- Teams can quickly find each skill by topic
- Implementations become more reliable, testable, and maintainable
