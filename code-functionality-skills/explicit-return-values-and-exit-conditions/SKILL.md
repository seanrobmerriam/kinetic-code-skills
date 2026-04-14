---
name: explicit-return-values-and-exit-conditions
description: Every code path must have an explicit return value or exit condition. Functions must not reach the end without returning. If a function should return nothing, explicitly return undefined, null, or void.
---
# Explicit Return Values and Exit Conditions


# Overview

Every code path must have an explicit return value or exit condition. Functions must not reach the end without returning. If a function should return nothing, explicitly return undefined, null, or void.

## When to Apply

- All function definitions
- Conditional branches
- Loop constructs
- Error handlers

## Examples

### ✓ Correct - Explicit Returns
```javascript
function findUserById(userId) {
    if (!userId) {
        return null;  // Explicit: not found
    }
    
    const user = database.find(userId);
    
    if (user) {
        return user;  // Explicit: user found
    }
    
    return null;  // Explicit: not found
}

function processData(data) {
    if (!data) {
        console.error('No data provided');
        return;  // Explicit: nothing to return
    }
    
    const result = transform(data);
    return result;  // Explicit: processed data
}
```

### ❌ Incorrect - Implicit/Missing Returns
```javascript
function findUserById(userId) {
    const user = database.find(userId);
    if (user) {
        return user;
    }
    // Missing: no return if user not found
    // Function returns undefined implicitly
}

function processData(data) {
    if (data) {
        const result = transform(data);
        // Missing: no explicit return
    }
}
```

## Rationale

Explicit returns make code behavior predictable and clear. Implicit returns hide bugs where code doesn't behave as intended. They make code easier to maintain and reduce debugging time.

## Verification

- Every code path has explicit return
- Return values match function contract
- No "fall through" to implicit undefined
- Return types are consistent
- All branches tested





## Common Failure Patterns

- Multiple return statements scattered throughout a function without clear intent.
- Returning different types from different code paths (sometimes an object, sometimes null).
- Forgetting to return from early-exit guard clauses, causing the function to continue.
- Nested conditionals that make it unclear which return will execute.

## Design Patterns to Apply

- **Single Entry, Single Exit (SESE)**: Each function has one entry point and one exit point for clearer control flow.
- **Result Object Pattern**: Return structured `{ ok, data, error }` objects instead of throwing or returning null.
- **Guard Clause Refactoring**: Replace nested conditionals with early returns to make exit paths explicit.

## Implementation Playbook

1. **Audit every function** — List all branches and ensure each has an explicit return.
2. **Standardize return types** — Decide whether functions return values, objects, or nothing (void).
3. **Use guard clauses** — Handle invalid/early-exit cases first with immediate returns.
4. **Eliminate implicit undefined** — If a function can fail, return null or a Result object explicitly.
5. **Mark void functions clearly** — Functions that perform actions without returning values should be clearly named (e.g., `processX()`, `sendY()`).

## Contract Template

```typescript
// Returns Result<T> instead of throwing
function findUserById(id: string): Result<User, 'NOT_FOUND' | 'INVALID_ID'> {
  if (!id || typeof id !== 'string') {
    return { ok: false, error: 'INVALID_ID' };
  }
  const user = database.find(id);
  if (!user) {
    return { ok: false, error: 'NOT_FOUND' };
  }
  return { ok: true, data: user };
}
```

## Code Review Prompts

- Can I trace every possible execution path and confirm it has an explicit return?
- Does the function always return the same type regardless of which branch executes?
- Are early-exit guard clauses free from side effects that might be skipped?

## Skill Deepening Notes

Mastering 'Explicit Return Values and Exit Conditions' requires understanding that implicit returns are hidden bugs. When a function reaches its end without an explicit return, it produces `undefined`—a value that often causes subtle failures far from the actual problem. Every branch, every conditional, and every error handler must be deliberate about what it returns. This skill is foundational for building predictable, debuggable systems where the behavior is obvious from reading the code alone.
