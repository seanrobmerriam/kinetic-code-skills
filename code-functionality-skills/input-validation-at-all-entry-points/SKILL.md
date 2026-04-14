---
name: input-validation-at-all-entry-points
description: All functions, endpoints, and external interfaces MUST validate inputs before processing. Invalid inputs must be rejected with clear error messages describing what was wrong and what format is expected.
---
# Input Validation at All Entry Points


# Overview

All functions, endpoints, and external interfaces MUST validate inputs before processing. Invalid inputs must be rejected with clear error messages describing what was wrong and what format is expected.

## When to Apply

- User input from forms or APIs
- Data from external services
- Parameters passed to public functions
- Command-line arguments
- File uploads
- Configuration values

## Examples

### ✓ Correct - Input Validation
```javascript
function createUser(name, email, age) {
    // Validate all inputs
    if (!name || typeof name !== 'string' || name.trim().length === 0) {
        throw new Error('Name is required and must be a non-empty string');
    }
    
    if (!email || !email.includes('@')) {
        throw new Error('Email must be valid (include @)');
    }
    
    if (typeof age !== 'number' || age < 0 || age > 150) {
        throw new Error('Age must be a number between 0 and 150');
    }
    
    // Only process if validation passes
    return saveUser({ name: name.trim(), email, age });
}
```

### ❌ Incorrect - No Validation
```javascript
function createUser(name, email, age) {
    // No validation - accepts anything
    return saveUser({ name, email, age });
}
```

## Rationale

Input validation prevents invalid data from corrupting your system. It catches errors early, prevents security vulnerabilities (injection attacks), and provides better user experience through clear error messages.

## Verification

- All public functions validate inputs
- Error messages specify what was wrong
- No valid data is rejected
- Invalid data is always rejected
- All edge cases tested

## Common Failure Patterns

- Accepting user input at face value without type or format verification.
- Validating only the happy path and missing null/empty inputs.
- Validating at the boundary of your system rather than at every entry point.
- Allowing malicious inputs to pass through that could cause injection or overflow.

## Design Patterns to Apply

- **Validator Pattern**: Create dedicated validation functions that return error objects rather than throwing, enabling composition.
- **Schema-Based Validation**: Use Zod, Joi, or TypeScript discriminated unions to define strict input contracts.
- **Fail-Fast Guard Clauses**: Validate inputs at the very start of functions and return errors immediately.

## Implementation Playbook

1. **Identify all ingress points** — APIs, CLI arguments, message queues, file uploads, and internal function calls.
2. **Define validation schemas** — Create reusable validators for each input type (email, UUID, date, etc.).
3. **Apply fail-fast validation** — Check inputs before any business logic executes.
4. **Return structured errors** — Include field name, received value, and expected format in error messages.
5. **Log rejected inputs** — Record validation failures for security auditing without leaking sensitive data.

## Contract Template

```typescript
function createUser(rawInput: unknown): Result<User, ValidationError> {
  const parsed = UserSchema.safeParse(rawInput);
  if (!parsed.success) {
    return { ok: false, errors: parsed.error.flatten() };
  }
  return { ok: true, data: parsed.data };
}
```

## Code Review Prompts

- Does this function validate the type, format, range, and constraints of every input?
- Are validation error messages specific enough to guide the caller toward correct usage?
- Could a malicious caller bypass validation through an alternate entry point?

## Skill Deepening Notes

Mastering 'Input Validation at All Entry Points' requires understanding that every piece of data entering your system is a potential attack vector or source of corruption. Validation is not optional overhead—it is the first line of defense. Sanitizing too late (after business logic has already processed raw data) defeats the purpose. A robust validation strategy treats all external data as untrusted until proven valid, and treats all validation failures as security events worth logging and monitoring.
