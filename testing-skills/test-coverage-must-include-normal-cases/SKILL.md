---
name: test-coverage-must-include-normal-cases
description: Write tests covering normal, expected use cases with valid inputs. Tests must verify that code works correctly under typical conditions. Include multiple examples of normal usage to ensure consistency.
---
# Test Coverage Must Include Normal Cases


# Overview

Write tests covering normal, expected use cases with valid inputs. Tests must verify that code works correctly under typical conditions. Include multiple examples of normal usage to ensure consistency.

## When to Apply

- All functions and methods
- All API endpoints
- All user workflows
- Any new feature
- Complex business logic

## Examples

### ✓ Correct - Normal Case Coverage
```javascript
describe('User.register()', () => {
    test('creates new user with valid data', () => {
        const user = User.register({
            name: 'John Doe',
            email: 'john@example.com',
            password: 'SecurePass123!'
        });
        
        expect(user.id).toBeDefined();
        expect(user.name).toBe('John Doe');
        expect(user.email).toBe('john@example.com');
        expect(user.createdAt).toBeDefined();
    });
    
    test('creates user with minimal valid data', () => {
        const user = User.register({
            name: 'Jane',
            email: 'jane@example.com',
            password: 'Pass1234!'
        });
        
        expect(user).toBeDefined();
    });
    
    test('multiple users can be created independently', () => {
        const user1 = User.register({
            name: 'User1',
            email: 'user1@example.com',
            password: 'Pass1234!'
        });
        
        const user2 = User.register({
            name: 'User2',
            email: 'user2@example.com',
            password: 'Pass1234!'
        });
        
        expect(user1.id).not.toBe(user2.id);
        expect(user1.email).not.toBe(user2.email);
    });
});
```

### ❌ Incorrect - Missing Normal Cases
```javascript
describe('User.register()', () => {
    test('rejects invalid password', () => {
        expect(() => User.register({
            name: 'John',
            email: 'john@example.com',
            password: 'weak'
        })).toThrow();
    });
    // Only tests error case, never tests successful registration
});
```

## Rationale

Normal cases represent the majority of code execution. If normal cases fail, the feature is broken. Tests must verify the feature works under typical conditions before testing edge cases.

## Verification

- Happy path tested with multiple examples
- Normal data produces expected results
- State changes correctly under normal conditions
- Output matches specification for typical inputs
- Multiple normal scenarios tested





## Common Failure Patterns

- Testing only error paths and neglecting the common success scenarios.
- Using a single "happy path" test to cover a wide range of valid inputs.
- Not verifying that the returned data structure matches what callers expect.
- Skipping multi-step workflows that represent typical user journeys.

## Test Design Framework

- **Happy Path Suite**: A dedicated set of tests covering the primary success scenarios with varied valid inputs.
- **Data-Driven Tests**: Parameterize tests with multiple valid inputs to ensure consistent behavior.
- **Workflow Tests**: End-to-end tests that mimic real user stories from start to finish.

## Expanded Test Checklist

1. Identify all typical usage scenarios from user stories and specs.
2. Write tests for each scenario using valid, representative inputs.
3. Vary inputs within valid ranges to ensure consistent behavior (e.g., short/long strings, different number magnitudes).
4. Verify returned data structures have the expected shape and values.
5. Test multi-step workflows that represent common user journeys.

## Advanced Example: Full-Path Testing

```typescript
describe('User.register() - normal cases', () => {
  test('registers user with typical valid data', () => {
    const user = User.register({
      name: 'John Doe',
      email: 'john@example.com',
      password: 'SecurePass123!'
    });
    expect(user.id).toBeDefined();
    expect(user.name).toBe('John Doe');
    expect(user.email).toBe('john@example.com');
  });

  test('registers user with minimal valid data', () => {
    const user = User.register({
      name: 'Jane',
      email: 'jane@example.com',
      password: 'Pass1234!'
    });
    expect(user).toBeDefined();
    expect(user.isActive()).toBe(true);
  });

  test('multiple registrations produce independent users', () => {
    const user1 = User.register({ name: 'User1', email: 'user1@test.com', password: 'Pass1234!' });
    const user2 = User.register({ name: 'User2', email: 'user2@test.com', password: 'Pass1234!' });
    expect(user1.id).not.toBe(user2.id);
  });
});
```

## Code Review Prompts

- Are all primary user workflows tested with valid inputs?
- Do the tests cover a representative range of valid inputs, not just one example?
- Does the test verify the complete returned structure, not just that it didn't throw?

## Skill Deepening Notes

Mastering 'Test Coverage Must Include Normal Cases' requires understanding that normal cases represent the majority of how your code is actually used. If normal cases fail, users experience a broken feature even though the code "technically works" for edge cases. Normal case tests validate that the feature delivers its intended value under typical conditions, which is what users first encounter.
