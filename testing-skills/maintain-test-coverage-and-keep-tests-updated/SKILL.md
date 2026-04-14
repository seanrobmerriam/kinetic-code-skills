---
name: maintain-test-coverage-and-keep-tests-updated
description: Track test coverage and maintain it above 80% minimum. Update tests when code changes. Remove tests that no longer apply. Add tests for new features. Review and refactor tests alongside code reviews.
---
# Maintain Test Coverage and Keep Tests Updated


# Overview

Track test coverage and maintain it above 80% minimum. Update tests when code changes. Remove tests that no longer apply. Add tests for new features. Review and refactor tests alongside code reviews.

## When to Apply

- Every code change
- Every new feature
- Code refactoring
- Regular maintenance
- Before deployment
- Regular coverage reviews

## Examples

### ✓ Correct - Test Maintenance
```javascript
// Track coverage metrics
describe('User module', () => {
    // Calculate: 15 tests / ~18 code paths = 83% coverage
    
    test('creates user', () => { /* ... */ });
    test('rejects invalid email', () => { /* ... */ });
    test('rejects duplicate email', () => { /* ... */ });
    test('hashes password on creation', () => { /* ... */ });
    test('validates password strength', () => { /* ... */ });
    test('updates user profile', () => { /* ... */ });
    test('prevents unauthorized updates', () => { /* ... */ });
    test('deletes user account', () => { /* ... */ });
    test('deactivates instead of deleting', () => { /* ... */ });
    test('retrieves user by ID', () => { /* ... */ });
    test('searches users by name', () => { /* ... */ });
    test('handles pagination in search', () => { /* ... */ });
    test('returns 404 for non-existent user', () => { /* ... */ });
    test('authenticates with correct password', () => { /* ... */ });
    test('rejects incorrect password', () => { /* ... */ });
    // Coverage report: 83%
});

// When code changes, tests are updated
// Old code: simpleFunction(a, b) { return a + b; }
// Test removed when function deleted

// New code added
// New function: calculateDiscount(amount, percent)
// New test added to verify new feature

// Refactoring code? Tests updated to match
// Code refactored but behavior unchanged
// Tests pass with refactored code
```

### ❌ Incorrect - Tests Not Maintained
```javascript
describe('User module', () => {
    test('creates user', () => { /* ... */ });
    test('processes payment', () => { /* ... */ });  // Code removed but test still exists
    
    // New code added but no tests written
    // Coverage drops to 45%
    
    // Code behavior changed but old tests never updated
    // Some tests fail silently
    // Team stops running tests
});
```

## Rationale

Unmaintained tests become liabilities. Coverage drops over time. Tests stop being useful if they're not kept current. Regular maintenance ensures tests provide value.

## Verification

- Coverage tracked and reported
- Coverage above 80% minimum
- Tests updated when code changes
- Outdated tests removed
- New features have tests
- Tests run before deployment
- Coverage trending upward



## Common Failure Patterns

- Coverage dropping below threshold without awareness or consequence.
- Tests that pass but no longer test anything meaningful due to code refactoring.
- New code shipped without corresponding new tests.
- Test files that are never run as part of the CI pipeline.

## Test Design Framework

- **Coverage Enforcement**: Block CI/CD pipelines when coverage drops below threshold.
- **Coverage Reports**: Generate reports per module to identify under-tested areas.
- **Test Ownership**: Assign each module's tests to the same owner as the code.
- **Regular Audits**: Review test quality and relevance during code reviews, not just coverage metrics.

## Expanded Test Checklist

1. Set a minimum coverage threshold (e.g., 80%) and block merges below it.
2. Generate per-module coverage reports to identify gaps.
3. Before shipping a feature, confirm new code has corresponding tests.
4. Remove or update tests when code is deleted or refactored.
5. Track coverage trend over time—it should be increasing, not stagnant or declining.

## Advanced Example: Full-Path Testing

```typescript
// coverage.config.js
module.exports = {
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    },
    './src/services/': {
      branches: 90,
      functions: 90,
      lines: 90,
      statements: 90
    }
  }
};

// Test file lifecycle
describe('UserService', () => {
  // When new method added: add tests immediately
  test('resetPassword sends reset email', async () => { ... });

  // When method removed: remove tests immediately
  // (old test for deprecated method deleted)

  // When behavior changed: update tests to match
  test('login requires email verification', async () => { ... }); // Updated from previous behavior
});
```

## Code Review Prompts

- Is coverage being tracked and reported per module?
- Does coverage trend upward over time, or has it plateaued?
- When code is deleted or refactored, are corresponding tests removed or updated?

## Skill Deepening Notes

Mastering 'Maintain Test Coverage and Keep Tests Updated' requires understanding that test coverage is a living metric. A codebase with 90% coverage last year but 60% today is not actually at 90% coverage—it's at 60% with a stale report. Tests must evolve with the code they verify; otherwise they become misleading documentation that suggests safety where there is none. Coverage metrics without maintenance discipline create false confidence.
