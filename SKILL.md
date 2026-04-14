---
name: isolate-tests-from-external-dependencies
description: Tests must not depend on external systems, databases, or network calls. Mock external dependencies. Use test data, not production data. Tests must be independent and repeatable. Running tests multiple times must produce same results.
---

# Kinetic Code Necessary Skills

## Overview

This skill combines 20 individual coding quality skills:

- 10 code functionality skills
- 10 testing skills

The skill is organized so each individual skill has its own `SKILL.md` file in one of these directories:

- `/code-functionality-skills`
- `/testing-skills`

---

## How to Use

This skill is designed to improve code quality across two dimensions: writing robust code and testing it thoroughly.

### For Individual Developers

1. **Select a Skill**: Choose a skill from the lists below that matches the code challenge or situation you're facing (e.g., "handling errors," "validating inputs," "testing edge cases")
2. **Read the Skill File**: Navigate to the skill's `SKILL.md` file in either `/code-functionality-skills` or `/testing-skills`
3. **Apply the Checklist**: Use the skill's checklist and guidance to implement or review your code
4. **Verify Against Criteria**: Run tests or code review against the skill's requirements before shipping

### For Teams

1. **Adopt as a Code Standard**: Reference this skill pack in your pull request checklist or coding standards
2. **Link During Reviews**: When reviewing PRs, cite the relevant skill if code violates its principles
3. **Share Skill References**: Help team members navigate to the specific skill that describes the issue they're working on
4. **Build Habits**: Rotate through skills in team meetings to build shared understanding

### For Debugging and Incident Response

- **Post-Mortem**: When a bug occurs, identify which skill(s) could have prevented it, then review that skill's checklist
- **Root Cause**: Use the quick reference table to find which coding or testing principle was missed
- **Future Prevention**: Add the failing skill to your team's next focus area

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

## DO's and DON'Ts

### DO ✓

- **DO use these skills in sequence**: Apply functionality checklist first (input → processing → output), then testing checklist (coverage → edge cases → integration)
- **DO read the individual skill files**: Each has deeper context, examples, and edge cases beyond this summary
- **DO customize the checklists**: Adapt the skills to your language, framework, or domain-specific constraints
- **DO reference skills by name** in pull request comments and team discussions
- **DO combine multiple skills**: Many defects are caught by combining skills (e.g., input validation + state verification + error handling)
- **DO keep tests alongside functionality skills**: Write tests *while* implementing, not after
- **DO use the quick reference table** when you're unsure if your code handles a specific case
- **DO review this pack regularly**: Business needs, technologies, and team composition change—revisit quarterly

### DON'T ✗

- **DON'T skip testing skills** because "it's a small change"—even small changes break contracts
- **DON'T memorize the checklist**: Understanding *why* each skill matters is more valuable than memorization
- **DON'T apply all skills superficially**: Better to apply 3 skills deeply than 20 skills as box-checking
- **DON'T treat skills as optional**:  Each one prevents a specific class of production bugs
- **DON'T ignore error cases**: "It will never happen" is where most failures hide
- **DON'T write tests after code ships**: Testing must inform design, not validate it after the fact
- **DON'T skip state verification**: Stateful systems fail silently if preconditions aren't checked
- **DON'T use this skill pack in isolation** from your team's existing standards, architecture, or review process
- **DON'T assume edge cases are rare**: Off-by-one, null, empty collection, and timeout bugs are among the most common

---

## Success Criteria

This skill pack is working as intended when:

- Each of the 20 skills exists as a standalone `SKILL.md`
- Functionality and testing skills are separated into their own directories
- Teams can quickly find each skill by topic
- Implementations become more reliable, testable, and maintainable
