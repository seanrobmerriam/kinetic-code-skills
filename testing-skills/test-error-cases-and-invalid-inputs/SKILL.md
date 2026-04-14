---
name: test-error-cases-and-invalid-inputs
description: Write tests for error handling. Test that invalid inputs are rejected appropriately. Test that operations fail gracefully when preconditions aren't met. Verify error messages are helpful and clear.
---
# Test Error Cases and Invalid Inputs


# Overview

Write tests for error handling. Test that invalid inputs are rejected appropriately. Test that operations fail gracefully when preconditions aren't met. Verify error messages are helpful and clear.

## When to Apply

- Input validation
- Permission checks
- State verification
- Resource availability
- External service failures
- All error paths

## Examples

### ✓ Correct - Error Case Coverage
```javascript
describe('User.register()', () => {
    // Missing required fields
    test('rejects registration with missing name', () => {
        const error = () => User.register({
            email: 'john@example.com',
            password: 'SecurePass123!'
            // Missing name
        });
        
        expect(error).toThrow('Name is required');
    });
    
    // Invalid format
    test('rejects invalid email format', () => {
        const error = () => User.register({
            name: 'John',
            email: 'not-an-email',  // Invalid format
            password: 'SecurePass123!'
        });
        
        expect(error).toThrow('Invalid email format');
    });
    
    // Weak password
    test('rejects weak password', () => {
        const error = () => User.register({
            name: 'John',
            email: 'john@example.com',
            password: 'weak'  // Too short, no complexity
        });
        
        expect(error).toThrow('Password must be at least 8 characters');
    });
    
    // Duplicate email
    test('rejects duplicate email address', () => {
        User.register({
            name: 'John',
            email: 'john@example.com',
            password: 'SecurePass123!'
        });
        
        const error = () => User.register({
            name: 'Jane',
            email: 'john@example.com',  // Duplicate
            password: 'SecurePass123!'
        });
        
        expect(error).toThrow('Email already registered');
    });
    
    // Type errors
    test('rejects non-string inputs', () => {
        expect(() => User.register({
            name: 123,  // Should be string
            email: 'john@example.com',
            password: 'SecurePass123!'
        })).toThrow('Name must be a string');
    });
});

describe('File.delete()', () => {
    // Non-existent file
    test('throws error when file does not exist', () => {
        expect(() => File.delete('/non/existent/file.txt'))
            .toThrow('File not found');
    });
    
    // Permission denied
    test('throws error without delete permission', () => {
        expect(() => File.delete('/system/protected/file.txt'))
            .toThrow('Permission denied');
    });
    
    // Invalid path
    test('throws error with invalid path', () => {
        expect(() => File.delete(''))
            .toThrow('Invalid file path');
    });
});
```

### ❌ Incorrect - Missing Error Cases
```javascript
describe('User.register()', () => {
    test('registers user', () => {
        const user = User.register({
            name: 'John',
            email: 'john@example.com',
            password: 'SecurePass123!'
        });
        expect(user).toBeDefined();
    });
    // Never tests what happens with invalid inputs
});
```

## Rationale

Error handling is critical for reliability. Untested error cases lead to unexpected behavior or crashes. Testing ensures errors are caught and handled gracefully.

## Verification

- All validation errors tested
- All permission checks tested
- Invalid input formats tested
- Boundary violations tested
- Error messages are helpful
- Graceful failure verified
## Common Failure Patterns

- Only testing the happy path and skipping error scenario coverage entirely.
- Not verifying that error messages are specific and actionable for the caller.
- Swallowing exceptions in tests without asserting on the error type or message.
- Missing validation of malformed inputs (wrong types, format violations, size limits).

## Test Design Framework

- **Negative Test Suite**: Dedicated test files for invalid inputs, organized by error category.
- **Error Contract Tests**: Assert that errors have specific types, codes, and human-readable messages.
- **Fuzzing**: Generate random malformed inputs to discover unhandled error cases.

## Expanded Test Checklist

1. List every validation rule and ensure there is a test that violates it.
2. Test with null, undefined, and wrong types for every parameter.
3. Test with values that are too small, too large, empty, or malformed.
4. Assert that thrown errors have the expected type and message.
5. Verify that the system remains stable after catching an error—no corrupted state.

## Advanced Example: Full-Path Testing

```typescript
describe('User.register() - error cases', () => {
  test('rejects registration with missing name', () => {
    expect(() => User.register({
      email: 'john@example.com',
      password: 'SecurePass123!'
    })).toThrow('Name is required');
  });

  test('rejects invalid email format', () => {
    expect(() => User.register({
      name: 'John',
      email: 'not-an-email',
      password: 'SecurePass123!'
    })).toThrow('Invalid email format');
  });

  test('rejects weak password', () => {
    expect(() => User.register({
      name: 'John',
      email: 'john@example.com',
      password: 'weak'
    })).toThrow('Password must be at least 8 characters');
  });

  test('rejects duplicate email', () => {
    User.register({ name: 'John', email: 'john@example.com', password: 'Pass1234!' });
    expect(() => User.register({
      name: 'Jane',
      email: 'john@example.com',
      password: 'Pass1234!'
    })).toThrow('Email already registered');
  });
});
```

## Code Review Prompts

- Does each validation rule have a corresponding test that violates it?
- Are error messages specific enough to guide a developer toward the fix?
- Does the system remain stable after catching and handling each error?

## Skill Deepening Notes

Mastering 'Test Error Cases and Invalid Inputs' requires understanding that a feature that works perfectly with valid inputs but fails catastrophically with invalid ones is a broken feature. Error handling defines how your system behaves when things go wrong—and things always go wrong. Users encounter invalid inputs, network failures, and unexpected states regularly. Graceful, informative error handling is what separates robust systems from fragile ones.
