---
name: verify-both-happy-path-and-error-paths
description: Write tests for both successful operations and failure cases. Test that success produces expected results AND that failures are handled gracefully. Never assume operations succeed - test the failure path too.
---
# Verify Both Happy Path and Error Paths


# Overview

Write tests for both successful operations and failure cases. Test that success produces expected results AND that failures are handled gracefully. Never assume operations succeed - test the failure path too.

## When to Apply

- All operations with error paths
- API integrations (success and failure)
- Database operations
- File operations
- External service calls
- Validation and rejection paths

## Examples

### ✓ Correct - Happy and Error Path Testing
```javascript
describe('Payment processing', () => {
    // HAPPY PATH: successful payment
    test('successfully processes valid payment', async () => {
        const result = await processPayment({
            cardNumber: '4111111111111111',
            amount: 100,
            currency: 'USD'
        });
        
        expect(result.success).toBe(true);
        expect(result.transactionId).toBeDefined();
        expect(result.amount).toBe(100);
    });
    
    // ERROR PATH: card declined
    test('handles declined card gracefully', async () => {
        const result = await processPayment({
            cardNumber: '4000000000000002',  // Test card that declines
            amount: 100,
            currency: 'USD'
        });
        
        expect(result.success).toBe(false);
        expect(result.error).toBe('Card declined');
        expect(result.transactionId).toBeUndefined();
    });
    
    // ERROR PATH: invalid amount
    test('rejects payment with invalid amount', async () => {
        expect(() => processPayment({
            cardNumber: '4111111111111111',
            amount: -100,  // Invalid
            currency: 'USD'
        })).toThrow('Amount must be positive');
    });
    
    // ERROR PATH: missing card
    test('handles missing card gracefully', async () => {
        const result = await processPayment({
            cardNumber: null,
            amount: 100,
            currency: 'USD'
        });
        
        expect(result.success).toBe(false);
        expect(result.error).toBe('Card information required');
    });
});

describe('File operations', () => {
    // HAPPY PATH: file exists and is readable
    test('reads file successfully when file exists', () => {
        const content = readFile('/valid/path/file.txt');
        expect(content).toBeDefined();
        expect(content.length).toBeGreaterThan(0);
    });
    
    // ERROR PATH: file doesn't exist
    test('throws error when file does not exist', () => {
        expect(() => readFile('/non/existent/file.txt'))
            .toThrow('File not found');
    });
    
    // ERROR PATH: permission denied
    test('throws error with helpful message when permission denied', () => {
        expect(() => readFile('/system/protected/file.txt'))
            .toThrow('Permission denied: cannot read /system/protected/file.txt');
    });
});
```

### ❌ Incorrect - Only Tests Happy Path
```javascript
describe('Payment processing', () => {
    test('processes payment', async () => {
        const result = await processPayment({
            cardNumber: '4111111111111111',
            amount: 100,
            currency: 'USD'
        });
        
        expect(result.success).toBe(true);
        // Never tests what happens if payment fails
        // Never tests invalid inputs
        // Never tests error handling
    });
});
```

## Rationale

The happy path is just part of the story. Real systems must handle failures. Testing error paths ensures graceful degradation and robustness.

## Verification

- Happy path tested
- All error cases tested
- Error responses appropriate
- Error messages helpful
- System recovers from errors
- Both success and failure verified





## Common Failure Patterns

- Writing tests only for successful operations and assuming errors are handled.
- Not testing that error paths return correct HTTP status codes or error formats.
- Forgetting to test recovery behavior—what the system does after an error occurs.
- Asserting only on success booleans without verifying the actual result structure.

## Test Design Framework

- **Dual Coverage**: For every feature, write at least one success test and one failure test.
- **Error Response Contract**: Assert that errors return the expected structure (code, message, details).
- **Recovery Tests**: After an error, verify the system is in a stable state and can retry or continue.

## Expanded Test Checklist

1. List all outcomes for each operation—including success, validation errors, and system errors.
2. Write a test for the success outcome that verifies the complete result structure.
3. Write a test for each error outcome, asserting the error type, code, and message.
4. After error tests, verify the system state is clean and subsequent operations can proceed.
5. Test error paths that cross component boundaries to ensure errors propagate correctly.

## Advanced Example: Full-Path Testing

```typescript
describe('PaymentService.charge()', () => {
  test('returns transaction on successful charge', async () => {
    const result = await PaymentService.charge({ cardId: 'valid_card', amount: 100 });
    expect(result.ok).toBe(true);
    expect(result.transactionId).toBeDefined();
    expect(result.amount).toBe(100);
  });

  test('returns error when card is declined', async () => {
    const result = await PaymentService.charge({ cardId: 'declined_card', amount: 100 });
    expect(result.ok).toBe(false);
    expect(result.error).toBe('Card declined');
    expect(result.transactionId).toBeUndefined();
  });

  test('throws on invalid amount', async () => {
    await expect(PaymentService.charge({ cardId: 'valid_card', amount: -50 }))
      .rejects.toThrow('Amount must be positive');
  });

  test('returns error when card not found', async () => {
    const result = await PaymentService.charge({ cardId: 'nonexistent', amount: 100 });
    expect(result.ok).toBe(false);
    expect(result.error).toBe('Card not found');
  });
});
```

## Code Review Prompts

- For every operation, is there at least one success test and one failure test?
- Do error responses have the correct structure with specific error codes?
- After an error occurs, is the system in a stable state that can handle subsequent operations?

## Skill Deepening Notes

Mastering 'Verify Both Happy Path and Error Paths' requires understanding that production systems spend as much time in error states as in success states. A system that handles success gracefully but craters on edge cases is not a robust system—it's a fragile one wearing a success mask. Testing both paths ensures that when things go wrong (and they will), the system degrades gracefully rather than catastrophically.
