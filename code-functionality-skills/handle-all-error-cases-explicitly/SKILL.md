---
name: handle-all-error-cases-explicitly
description: Every error that could occur must be handled explicitly. Don't assume operations succeed. Wrap risky operations in try-catch blocks or use error checking. Propagate errors upward with context.
---
# Handle All Error Cases Explicitly


# Overview

Every error that could occur must be handled explicitly. Don't assume operations succeed. Wrap risky operations in try-catch blocks or use error checking. Propagate errors upward with context.

## When to Apply

- File operations (read, write, delete)
- Network requests
- Database operations
- Third-party API calls
- Type conversions
- Array/object access
- Any operation that could fail

## Examples

### ✓ Correct - Explicit Error Handling
```javascript
async function fetchUserData(userId) {
    try {
        // Risk: network request could fail
        const response = await fetch(`/api/users/${userId}`);
        
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }
        
        // Risk: JSON parsing could fail
        const data = await response.json();
        
        // Risk: data might be malformed
        if (!data.id || !data.name) {
            throw new Error('Invalid user data: missing required fields');
        }
        
        return data;
    } catch (error) {
        // Handle error with context
        console.error(`Failed to fetch user ${userId}:`, error.message);
        throw new Error(`User data unavailable: ${error.message}`);
    }
}
```

### ❌ Incorrect - Assumes Success
```javascript
async function fetchUserData(userId) {
    // No error handling - assumes everything works
    const response = await fetch(`/api/users/${userId}`);
    const data = await response.json();
    
    // What if response fails? What if JSON is invalid?
    // What if data is malformed?
    return data;
}
```

## Rationale

Errors happen in real systems. Not handling them means silent failures, corrupted state, and difficult debugging. Explicit error handling makes systems robust and reliable.

## Verification

- Every operation that could fail has error handling
- Error messages provide context
- Errors are logged appropriately
- System recovers gracefully from errors
- Error cases are tested





## Common Failure Patterns

- Catching generic `Exception` instead of specific error types, losing context.
- Swallowing errors silently with empty catch blocks or catch blocks that only log.
- Throwing raw errors upward without wrapping them in context-specific error types.
- Assuming external services or network calls will always succeed.

## Design Patterns to Apply

- **Try-Catch with Context**: Wrap risky operations and attach context in the catch block before rethrowing.
- **Error Aggregation**: Collect multiple errors during batch operations instead of failing on the first one.
- **Circuit Breaker**: Prevent cascading failures by stopping calls to a failing external service.
- **Sentinel Errors**: Define specific error types/codes (e.g., `INVALID_USER_INPUT`, `RESOURCE_NOT_FOUND`) for programmatic handling.

## Implementation Playbook

1. **Identify failure points** — List every operation that accesses the network, filesystem, database, or external services.
2. **Wrap with try-catch** — Every failure point gets a try-catch that either handles the error or rethrows with context.
3. **Classify errors** — Distinguish between recoverable errors (retry), programming errors (fix the bug), and fatal errors (graceful shutdown).
4. **Preserve error chain** — Use `cause` properties or nested exceptions to preserve the original error stack.
5. **Log at the boundary** — Log errors once at the outermost handler, not at every level.

## Contract Template

```typescript
async function fetchUserData(userId: string): Promise<UserData> {
  try {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) {
      throw new ApiError('USER_FETCH_FAILED', response.status, await response.text());
    }
    return await response.json();
  } catch (error) {
    if (error instanceof ApiError) throw error;
    throw new ApiError('USER_FETCH_FAILED', 0, error.message, { cause: error });
  }
}
```

## Code Review Prompts

- Can every operation that could fail result in an unhandled exception?
- Are errors caught at the right level with enough context to debug?
- Is the error handling strategy consistent across the codebase?

## Skill Deepening Notes

Mastering 'Handle All Error Cases Explicitly' requires understanding that errors are first-class citizens in robust systems, not afterthoughts. A system that assumes success is a system that will fail catastrophically. Every I/O operation, every external call, every type conversion, and every array access is a potential failure point. Proper error handling means knowing what went wrong, where, why, and what the system should do about it—whether that is retrying, falling back to a default, or propagating a meaningful error to the user.
