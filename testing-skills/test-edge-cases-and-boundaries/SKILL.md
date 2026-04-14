---
name: test-edge-cases-and-boundaries
description: Write tests for boundary conditions and edge cases: empty inputs, zero, one, maximum, minimum, first, last. Test behavior at limits of valid ranges. Include cases just inside and just outside valid boundaries.
---
# Test Edge Cases and Boundaries


# Overview

Write tests for boundary conditions and edge cases: empty inputs, zero, one, maximum, minimum, first, last. Test behavior at limits of valid ranges. Include cases just inside and just outside valid boundaries.

## When to Apply

- Numeric operations (zero, negative, maximum)
- Collections (empty, single item, many items)
- String operations (empty string, very long strings)
- Date/time operations (today, far future, far past)
- Any operation with defined boundaries

## Examples

### ✓ Correct - Edge Case Coverage
```javascript
describe('Array.slice()', () => {
    // Empty array: boundary case
    test('returns empty array when slicing empty array', () => {
        expect([].slice(0, 1)).toEqual([]);
    });
    
    // Single element: minimum non-empty
    test('handles array with single element', () => {
        expect([1].slice(0, 1)).toEqual([1]);
        expect([1].slice(1, 2)).toEqual([]);
    });
    
    // Boundary: start equals end
    test('returns empty when start equals end', () => {
        expect([1, 2, 3].slice(1, 1)).toEqual([]);
    });
    
    // Boundary: negative indices
    test('handles negative indices correctly', () => {
        expect([1, 2, 3].slice(-2, -1)).toEqual([2]);
        expect([1, 2, 3].slice(-10, 2)).toEqual([1, 2]);
    });
    
    // Boundary: indices beyond length
    test('handles indices beyond array length', () => {
        expect([1, 2, 3].slice(1, 100)).toEqual([2, 3]);
        expect([1, 2, 3].slice(100, 200)).toEqual([]);
    });
});

describe('String.trim()', () => {
    // Empty string
    test('returns empty string when input is empty', () => {
        expect(''.trim()).toBe('');
    });
    
    // Only whitespace
    test('returns empty when string is only whitespace', () => {
        expect('   '.trim()).toBe('');
        expect('\t\n'.trim()).toBe('');
    });
    
    // No whitespace
    test('returns unchanged when no whitespace', () => {
        expect('hello'.trim()).toBe('hello');
    });
    
    // Whitespace boundaries
    test('removes whitespace from boundaries only', () => {
        expect('  hello world  '.trim()).toBe('hello world');
        expect('\thello\n'.trim()).toBe('hello');
    });
});
```

### ❌ Incorrect - Missing Edge Cases
```javascript
describe('Array.slice()', () => {
    test('slices array correctly', () => {
        expect([1, 2, 3, 4, 5].slice(1, 3)).toEqual([2, 3]);
    });
    // Only tests normal case, misses all edge cases
});
```

## Rationale

Edge cases often reveal bugs that normal cases miss. Many real-world bugs occur at boundaries, with empty/null inputs, or at limits of ranges.

## Verification

- Empty/zero cases tested
- Boundary values tested (min, max, limits)
- Just inside and just outside boundaries tested
- Off-by-one scenarios tested
- All constraint boundaries tested





## Common Failure Patterns

- Testing only boundary values without verifying normal operation on either side.
- Not testing empty collections, zero values, or other common defaults.
- Missing tests for the first and last elements in ordered collections.
- Failing to test behavior at the maximum and minimum representable values.

## Test Design Framework

- **Boundary Value Analysis**: Test at and just inside/outside each boundary of the input domain.
- **Equivalence Partitioning**: Group inputs into ranges that should behave identically and test representative values from each.
- **Empty/Zero Suite**: Explicit tests for empty strings, empty arrays, zero numbers, and null/undefined.

## Expanded Test Checklist

1. List all numeric boundaries (min, max, zero, negative, overflow thresholds).
2. Identify collection boundaries (empty, single element, first, last).
3. Test just inside and just outside each boundary to verify the boundary itself is correct.
4. Test at maximum limits (max array length, max string length, max recursion depth).
5. Test with null, undefined, and empty values for all optional inputs.

## Advanced Example: Full-Path Testing

```typescript
describe('Array.slice() - boundary cases', () => {
  test('returns empty array when slicing empty array', () => {
    expect([].slice(0, 1)).toEqual([]);
  });

  test('handles array with single element', () => {
    expect([1].slice(0, 1)).toEqual([1]);
    expect([1].slice(1, 2)).toEqual([]);
  });

  test('returns empty when start equals end', () => {
    expect([1, 2, 3].slice(1, 1)).toEqual([]);
  });

  test('handles negative indices', () => {
    expect([1, 2, 3].slice(-2, -1)).toEqual([2]);
  });

  test('handles indices beyond array length', () => {
    expect([1, 2, 3].slice(1, 100)).toEqual([2, 3]);
    expect([1, 2, 3].slice(100, 200)).toEqual([]);
  });
});
```

## Code Review Prompts

- Have all numeric boundaries (min, max, zero, negative) been tested?
- Are empty and single-element collections tested for every operation?
- Are indices beyond array bounds tested to verify graceful handling?

## Skill Deepening Notes

Mastering 'Test Edge Cases and Boundaries' requires understanding that most bugs occur at the extremes of input domains. Empty inputs, full capacities, maximum values, and boundary transitions are where systems break down because programmers naturally focus on the "normal" case. Rigorous boundary testing prevents off-by-one errors, overflow surprises, and unexpected empty-state behavior.
