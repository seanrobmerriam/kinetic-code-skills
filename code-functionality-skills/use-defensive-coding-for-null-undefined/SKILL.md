---
name: use-defensive-coding-for-null-undefined
description: Never assume variables, objects, or arrays exist. Always check for null/undefined before access. Use guard clauses to exit early when values are missing. Provide meaningful defaults only when appropriate.
---
# Use Defensive Coding for Null/Undefined


# Overview

Never assume variables, objects, or arrays exist. Always check for null/undefined before access. Use guard clauses to exit early when values are missing. Provide meaningful defaults only when appropriate.

## When to Apply

- Object property access
- Array element access
- Function parameters
- Database query results
- API responses
- External data sources

## Examples

### ✓ Correct - Defensive Coding
```javascript
function getUserEmail(user) {
    // Guard clause: check parameter exists
    if (!user) {
        return null;
    }
    
    // Defensive: check property exists
    if (!user.email) {
        return null;
    }
    
    // Safe to use
    return user.email.toLowerCase();
}

function getNestedValue(obj, path) {
    // Guard: check object exists
    if (!obj) return null;
    
    // Defensive: check each level
    const [key1, key2, key3] = path.split('.');
    
    if (!obj[key1]) return null;
    if (!obj[key1][key2]) return null;
    if (!obj[key1][key2][key3]) return null;
    
    return obj[key1][key2][key3];
}
```

### ❌ Incorrect - Assumes Existence
```javascript
function getUserEmail(user) {
    // Assumes user exists
    // Assumes user.email exists
    return user.email.toLowerCase();  // Crashes if user or email missing
}

function getNestedValue(obj, path) {
    // Assumes obj exists and all nested properties exist
    const [key1, key2, key3] = path.split('.');
    return obj[key1][key2][key3];  // Crashes if any level missing
}
```

## Rationale

Null/undefined errors are the most common runtime errors. Defensive coding prevents crashes and makes code robust. It makes bugs obvious rather than hidden.

## Verification

- All object access checked for existence
- All array access bounds-checked
- All parameters checked
- Clear defaults or error returns provided
- Null/undefined cases tested





## Common Failure Patterns

- Accessing nested properties without checking intermediate values exist (e.g., `user.address.city` crashes if `user.address` is null).
- Calling methods on objects that may be null without guard clauses.
- Iterating over arrays or collections without checking they are not empty.
- Assuming function parameters are always populated when they may be optional.

## Design Patterns to Apply

- **Guard Clause Pattern**: Check for null/undefined at the start of functions and exit early.
- **Optional Chaining (`?.`)**: Use language-native optional chaining to safely access nested properties.
- **Nullish Coalescing (`??`)**: Provide safe defaults only when null/undefined is semantically equivalent to "not provided."
- **Maybe/Option Type**: Model optional values explicitly using types like `Maybe<T>` or `Optional<T>`.

## Implementation Playbook

1. **Audit property access** — Identify all dot-notation property accesses that could fail if intermediate values are null.
2. **Add null guards** — Insert explicit null checks before any operation on potentially null values.
3. **Prefer early returns** — Handle the null case first and return early; only proceed with the happy path.
4. **Use optional chaining** — Replace `obj && obj.prop` with `obj?.prop` for cleaner code.
5. **Define null semantics** — Decide what null means in your domain (missing? unknown? invalid?) and handle accordingly.

## Contract Template

```typescript
function getNestedValue<T>(obj: Record<string, unknown>, path: string, defaultValue: T): T {
  if (!obj) return defaultValue;
  const keys = path.split('.');
  let current: unknown = obj;
  for (const key of keys) {
    if (current == null) return defaultValue;
    current = (current as Record<string, unknown>)[key];
  }
  return (current ?? defaultValue) as T;
}
```

## Code Review Prompts

- Could any property access in this code crash if an intermediate value is null or undefined?
- Are function parameters that could be optional explicitly marked and handled?
- Is there a consistent strategy for handling missing values—do we use defaults, errors, or optional types?

## Skill Deepening Notes

Mastering 'Use Defensive Coding for Null/Undefined' requires understanding that null and undefined are not just values—they represent the absence of a value and carry semantic meaning about the state of your data. Defensive coding treats every potentially-absent value as a risk to be managed, not an assumption to be made. In JavaScript/TypeScript, where types can be flexible and data structures unpredictable, guard clauses and optional chaining are essential tools for preventing the "Cannot read property of undefined" errors that constitute a significant portion of runtime bugs.
