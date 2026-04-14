---
name: test-expected-vs-actual-behavior
description: Don't assume code works as intended. Test actual behavior against expected behavior. Write tests for normal cases, edge cases, and error cases. Test boundaries, limits, and constraints. Verify output matches specification.
---
# Test Expected vs Actual Behavior


# Overview

Don't assume code works as intended. Test actual behavior against expected behavior. Write tests for normal cases, edge cases, and error cases. Test boundaries, limits, and constraints. Verify output matches specification.

## When to Apply

- All new functions
- All public APIs
- Complex logic
- Edge case handling
- Error handling
- State transitions

## Examples

### ✓ Correct - Behavior Testing
```javascript
// Test file: calculator.test.js
describe('Calculator.add()', () => {
    // Normal case
    test('adds two positive numbers correctly', () => {
        expect(add(2, 3)).toBe(5);
        expect(add(10, 20)).toBe(30);
    });
    
    // Negative numbers
    test('adds negative numbers correctly', () => {
        expect(add(-2, -3)).toBe(-5);
        expect(add(10, -5)).toBe(5);
    });
    
    // Edge cases
    test('handles zero correctly', () => {
        expect(add(0, 5)).toBe(5);
        expect(add(0, 0)).toBe(0);
    });
    
    // Error cases
    test('throws error for non-numeric inputs', () => {
        expect(() => add('2', 3)).toThrow();
        expect(() => add(null, 3)).toThrow();
    });
    
    // Boundary cases
    test('handles large numbers', () => {
        expect(add(Number.MAX_SAFE_INTEGER - 1, 1)).toBe(Number.MAX_SAFE_INTEGER);
    });
});

// Manual testing in code
function testUserCreation() {
    const user = createUser('John', 'john@example.com', 30);
    
    // Verify expected behavior
    assert(user.id > 0, 'User should have ID');
    assert(user.name === 'John', 'User name should match input');
    assert(user.createdAt instanceof Date, 'User should have creation date');
    assert(user.active === true, 'New user should be active');
}
```

### ❌ Incorrect - No Behavior Testing
```javascript
// No tests - assumes code works
function add(a, b) {
    return a + b;
}

function createUser(name, email, age) {
    return { name, email, age };
}
// No verification that code actually works as intended
```

## Rationale

Untested code is assumed to work, but usually has bugs. Testing verifies actual behavior matches expected behavior. It catches bugs before users do.

## Verification

- Tests for normal cases exist
- Tests for edge cases exist
- Tests for error cases exist
- Tests for boundaries and limits exist
- All tests pass before deployment



## Common Failure Patterns

- Asserting implementation details rather than observable behavior, making tests fragile.
- Testing the absence of bugs rather than the presence of correct behavior.
- Writing tests that pass with incorrect implementations due to weak assertions.
- Not defining what "correct" means before writing the test, leading to vague assertions.

## Design Patterns to Apply

- **Specification-Driven Testing**: Write the expected behavior as executable specifications before coding.
- **Property-Based Testing**: For pure functions, generate random inputs and verify output properties hold.
- **Snapshot Testing**: Capture expected outputs for complex data structures and assert exact matches.
- **Behavior Assertions**: Assert on public outcomes (return values, state changes, emitted events) rather than internal state.

## Implementation Playbook

1. **Define the specification** — Before writing code, write what the function must do in plain language.
2. **Derive test cases from spec** — Each clause of the spec should map to at least one test case.
3. **Write assertions that verify outcomes** — Check return values, thrown error types, state changes, and side effects.
4. **Test happy path, edge cases, and error paths** — The spec covers all three categories.
5. **Refactor tests with code** — When implementation changes, update tests to still reflect the spec.

## Contract Template

```typescript
// Specification: calculateArea returns the area of a rectangle given width and height
// - width and height must be positive numbers
// - returns width * height
// - throws if either dimension is non-positive

function calculateArea(width: number, height: number): number {
  if (typeof width !== 'number' || typeof height !== 'number') {
    throw new TypeError('Width and height must be numbers');
  }
  if (width <= 0 || height <= 0) {
    throw new Error('Width and height must be positive');
  }
  return width * height;
}

// Tests derived from specification
describe('calculateArea', () => {
  test('returns correct area for positive dimensions', () => {
    expect(calculateArea(3, 4)).toBe(12);
    expect(calculateArea(10, 5)).toBe(50);
  });

  test('throws for zero or negative dimensions', () => {
    expect(() => calculateArea(0, 5)).toThrow('positive');
    expect(() => calculateArea(3, -1)).toThrow('positive');
  });

  test('throws for non-numeric inputs', () => {
    expect(() => calculateArea('3' as unknown as number, 4)).toThrow(TypeError);
  });
});
```

## Code Review Prompts

- Does each test assert on observable behavior rather than implementation details?
- Can the tests detect if the implementation is wrong, or would they pass for an incorrect implementation?
- Does each test cover a distinct case from the specification?

## Skill Deepening Notes

Mastering 'Test Expected vs Actual Behavior' requires understanding that tests are specifications of behavior, not validations of implementation. The question "does this code work?" is incomplete—correct code works for all valid inputs, fails gracefully for invalid inputs, and produces predictable outputs. A test that only checks the happy path with one input is not testing correctness; it is testing coincidence. True verification means defining what correct behavior looks like across the full input domain and ensuring the implementation matches.
