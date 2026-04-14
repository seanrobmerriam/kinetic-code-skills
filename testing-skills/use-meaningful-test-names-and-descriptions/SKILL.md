---
name: use-meaningful-test-names-and-descriptions
description: Test names must clearly describe what is being tested and what the expected outcome is. Names should be readable sentences explaining the behavior. A test name should be understandable without reading the test code.
---
# Use Meaningful Test Names and Descriptions


# Overview

Test names must clearly describe what is being tested and what the expected outcome is. Names should be readable sentences explaining the behavior. A test name should be understandable without reading the test code.

## When to Apply

- All test files
- All test cases
- Test descriptions
- Test comments

## Examples

### ✓ Correct - Clear Test Names
```javascript
describe('User.register()', () => {
    // Clear: what input, what expected outcome
    test('successfully registers user with valid email and password', () => {
        // test code
    });
    
    test('rejects registration with email already in use', () => {
        // test code
    });
    
    test('returns user object with id, name, and createdAt timestamp', () => {
        // test code
    });
    
    test('throws error with message explaining password is too weak', () => {
        // test code
    });
});

describe('Payment processing', () => {
    test('successfully charges card and creates transaction record', () => {
        // test code
    });
    
    test('rejects charge when card is declined', () => {
        // test code
    });
    
    test('returns transaction ID that matches database record', () => {
        // test code
    });
});

// Good: describes specific behavior
test('calculates discount correctly for purchases over $100', () => {});
test('rounds currency to exactly 2 decimal places', () => {});
test('handles leap years correctly when calculating age', () => {});

// Good: describes error behavior
test('throws error when attempting to delete non-existent record', () => {});
test('returns null instead of throwing when item not found', () => {});
```

### ❌ Incorrect - Unclear Test Names
```javascript
test('test user', () => {
    // Unclear: what is being tested?
});

test('works', () => {
    // Unclear: what works? Does what?
});

test('edge case', () => {
    // Which edge case? What's the expected behavior?
});

test('register', () => {
    // Success or failure? What input?
});

// Bad: doesn't explain the specific behavior being tested
test('email validation', () => {});
test('payment', () => {});
test('user creation', () => {});
```

## Rationale

Good test names serve as documentation. They make tests easier to understand, maintain, and debug. They clarify test intent and expected behavior.

## Verification

- Test name describes input and expected output
- Name is understandable without reading code
- Clear what behavior is being tested
- Easy to identify what failed
- Good names help maintain tests





## Common Failure Patterns

- Naming tests with implementation details ("test 1", "test 2") rather than behaviors.
- Using vague names like "handles error" without specifying which input or what error.
- Test names that don't indicate the expected outcome.
- Names that require reading the test body to understand what is being verified.

## Test Design Framework

- **Behavior-First Naming**: Name tests to describe the scenario and expected outcome, not the implementation.
- **Given-When-Then Structure**: "Given X, When Y, Then Z" style names make intent explicit.
- **Specification as Test Name**: Use the specification item directly as the test name.

## Expanded Test Checklist

1. Write the test name as a complete sentence describing the expected behavior.
2. Include the specific input values and the expected outcome in the name.
3. Avoid technical implementation details in names—describe WHAT should happen, not HOW.
4. For error cases, name the test after the invalid input and expected error.
5. Review test names with the team to ensure they serve as documentation.

## Advanced Example: Full-Path Testing

```typescript
// Bad: test('register', () => { ... })
// Good:
test('successfully registers user with valid email and password', () => { ... });

// Bad: test('error', () => { ... })
// Good:
test('rejects registration with email already in use', () => { ... });

// Bad: test('handles null input', () => { ... })
// Good:
test('throws Error when name is null', () => { ... });

// Full example
describe('Calculator.multiply()', () => {
  test('returns correct product for two positive integers', () => {
    expect(multiply(3, 4)).toBe(12);
  });

  test('returns negative result when multiplying positive by negative', () => {
    expect(multiply(3, -4)).toBe(-12);
  });

  test('throws error when multiplying by zero', () => {
    expect(() => multiply(5, 0)).toThrow('Multiplication by zero undefined');
  });
});
```

## Code Review Prompts

- Could someone understand what this test verifies without reading the code?
- Does the test name describe both the input scenario AND the expected outcome?
- Is the test name specific enough to identify which requirement is being tested?

## Skill Deepening Notes

Mastering 'Use Meaningful Test Names and Descriptions' requires understanding that tests are documentation. When a test fails, the first thing a developer reads is the test name. A good name immediately conveys what broke and under what circumstances. A bad name forces the developer to read test code to understand what was being tested. Test names should read like specifications: "it should do X when Y."
