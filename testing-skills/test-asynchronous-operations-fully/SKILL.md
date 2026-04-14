---
name: test-asynchronous-operations-fully
description: Test that async operations complete successfully. Verify promises resolve or reject correctly. Test timeout behavior. Test concurrent operations don't interfere. Wait for async completion before verifying results.
---
# Test Asynchronous Operations Fully


# Overview

Test that async operations complete successfully. Verify promises resolve or reject correctly. Test timeout behavior. Test concurrent operations don't interfere. Wait for async completion before verifying results.

## When to Apply

- All async functions
- API calls
- Database operations
- Promises and async/await
- Parallel operations
- Timeouts and delays

## Examples

### ✓ Correct - Async Testing
```javascript
describe('Async operations', () => {
    // Wait for promise resolution
    test('fetches user data successfully', async () => {
        const promise = fetchUser(1);
        
        const user = await promise;
        
        expect(user.id).toBe(1);
        expect(user.name).toBeDefined();
    });
    
    // Test error rejection
    test('rejects on fetch failure', async () => {
        const promise = fetchUser(-1);
        
        await expect(promise).rejects.toThrow('Invalid user ID');
    });
    
    // Test timeout
    test('times out if response takes too long', async () => {
        const promise = fetchUserWithTimeout(1, 100);  // 100ms timeout
        
        // Simulate slow server
        jest.advanceTimersByTime(150);
        
        await expect(promise).rejects.toThrow('Request timeout');
    });
    
    // Test concurrent operations
    test('handles concurrent requests correctly', async () => {
        const promise1 = fetchUser(1);
        const promise2 = fetchUser(2);
        const promise3 = fetchUser(3);
        
        const [user1, user2, user3] = await Promise.all([promise1, promise2, promise3]);
        
        expect(user1.id).toBe(1);
        expect(user2.id).toBe(2);
        expect(user3.id).toBe(3);
    });
    
    // Test retry logic
    test('retries on transient failure', async () => {
        let attemptCount = 0;
        const fetchWithRetry = async () => {
            attemptCount++;
            if (attemptCount < 3) {
                throw new Error('Temporary failure');
            }
            return { success: true };
        };
        
        const result = await retryAsync(fetchWithRetry, 3);
        
        expect(attemptCount).toBe(3);
        expect(result.success).toBe(true);
    });
});
```

### ❌ Incorrect - Missing Async Testing
```javascript
describe('Async operations', () => {
    test('fetches user data', () => {
        const promise = fetchUser(1);
        // Not waiting for promise - test completes immediately
        expect(promise).toBeDefined();  // Only tests promise object, not result
    });
    
    test('handles errors', () => {
        const promise = fetchUser(-1);
        // Not handling rejection - test passes even if error thrown
    });
});
```

## Rationale

Async bugs are subtle. Without proper testing, promises might resolve after test completes, timeouts might not trigger, concurrent operations might interfere. Proper async testing ensures reliability.

## Verification

- Promises awaited before verification
- Success paths tested
- Rejection paths tested
- Timeout behavior tested
- Concurrent operations tested
- Race conditions tested





## Common Failure Patterns

- Not awaiting async operations, causing tests to pass before assertions run.
- Forgetting to test promise rejection paths.
- Not testing timeout behavior when async operations hang.
- Assuming async operations complete in a deterministic order without testing concurrency.

## Test Design Framework

- **Async/Await Tests**: Use async/await syntax and always await before asserting.
- **Promise Exhaustiveness**: Test resolved, rejected, and pending states.
- **Concurrency Tests**: Test that parallel async operations don't interfere or produce race conditions.
- **Timeout Tests**: Verify the system responds correctly when operations exceed time limits.

## Expanded Test Checklist

1. Identify all async functions and ensure they are all awaited in tests.
2. Write tests for the resolved path with successful input.
3. Write tests for the rejected path with failure-inducing input.
4. Test concurrent operations with Promise.all to ensure correct interleaving.
5. Test timeout behavior by mocking clocks or artificially delaying responses.

## Advanced Example: Full-Path Testing

```typescript
describe('fetchUser() - async behavior', () => {
  test('resolves with user data on success', async () => {
    const user = await fetchUser(1);
    expect(user.id).toBe(1);
    expect(user.name).toBeDefined();
  });

  test('rejects with error when user not found', async () => {
    await expect(fetchUser(-1)).rejects.toThrow('Invalid user ID');
  });

  test('handles concurrent fetches correctly', async () => {
    const [user1, user2, user3] = await Promise.all([
      fetchUser(1),
      fetchUser(2),
      fetchUser(3)
    ]);
    expect(user1.id).toBe(1);
    expect(user2.id).toBe(2);
    expect(user3.id).toBe(3);
  });

  test('times out if response takes too long', async () => {
    jest.useFakeTimers();
    const promise = fetchUserWithTimeout(1, 100);
    jest.advanceTimersByTime(150);
    await expect(promise).rejects.toThrow('Request timeout');
  });
});
```

## Code Review Prompts

- Are all async operations properly awaited before assertions run?
- Do rejection paths have corresponding tests that verify the error type and message?
- Is timeout behavior tested to ensure the system fails gracefully?
- Are concurrent operations tested for correct behavior under parallel execution?

## Skill Deepening Notes

Mastering 'Test Asynchronous Operations Fully' requires understanding that async bugs are among the hardest to diagnose. A test that completes before an async operation resolves will pass vacuously, giving false confidence. Timeouts that never trigger, promises that resolve after test completion, and race conditions that only appear under load are all silent killers of system reliability. Full async testing means never assuming an async operation has completed—always awaiting, always asserting on the outcome.
