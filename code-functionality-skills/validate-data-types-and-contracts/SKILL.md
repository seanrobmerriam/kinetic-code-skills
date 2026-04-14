---
name: validate-data-types-and-contracts
description: Verify that data matches expected types and format. Check length constraints, value ranges, and required fields. Define clear contracts for what functions accept and return. Use type definitions or assertions to verify contracts.
---
# Validate Data Types and Contracts


# Overview

Verify that data matches expected types and format. Check length constraints, value ranges, and required fields. Define clear contracts for what functions accept and return. Use type definitions or assertions to verify contracts.

## When to Apply

- Function parameters
- API request/response data
- Database records
- Configuration values
- External data sources
- Type system boundaries

## Examples

### ✓ Correct - Type Validation
```javascript
// Type contract defined
function calculateDiscount(amount, discountPercent) {
    // Validate types
    if (typeof amount !== 'number' || amount < 0) {
        throw new Error('Amount must be a positive number');
    }
    
    if (typeof discountPercent !== 'number' || discountPercent < 0 || discountPercent > 100) {
        throw new Error('Discount percent must be 0-100');
    }
    
    // Contract verified: proceed with calculation
    const discount = amount * (discountPercent / 100);
    return amount - discount;
}

// Using TypeScript for compile-time checking
interface UserData {
    id: number;
    name: string;
    email: string;
}

function validateUser(data: UserData): boolean {
    return data.id > 0 && data.name.length > 0 && data.email.includes('@');
}
```

### ❌ Incorrect - No Type Validation
```javascript
function calculateDiscount(amount, discountPercent) {
    // No validation - assumes correct types
    const discount = amount * (discountPercent / 100);
    return amount - discount;  // Could return NaN or wrong result
}

function validateUser(data) {
    // No validation - assumes correct structure
    return data.id > 0 && data.name.length > 0;  // Crashes if data is null/wrong type
}
```

## Rationale

Type mismatches cause subtle bugs and runtime errors. Validating contracts ensures predictable behavior. It catches errors early when they're cheap to fix.

## Verification

- All inputs validated for type and format
- Type contracts clearly defined
- Range/length constraints checked
- All edge cases tested
- Type system used when available





## Common Failure Patterns

- Passing wrong types to functions that assume correct types (e.g., passing a string where a number is expected).
- Not validating structural contracts—objects missing required properties or having extra unknown properties.
- Ignoring type coercion pitfalls (e.g., `"5" + 2 === "52"` instead of `7`).
- Accepting arbitrary JSON without schema validation, leading to runtime type errors.

## Design Patterns to Apply

- **TypeScript Types/Interfaces**: Define explicit interfaces for all data structures crossing module boundaries.
- **Discriminated Unions**: Model mutually exclusive states (success/failure, loading/loaded/error) with union types.
- **Schema Validation Libraries**: Use Zod, io-ts, or similar to validate runtime data against compile-time types.
- **Branded Types**: Distinguish between primitive types that share the same representation (e.g., `UserId` vs `OrderId` both being strings).

## Implementation Playbook

1. **Define explicit contracts** — For every function, specify the exact types and structure of inputs and outputs.
2. **Validate at boundaries** — Check external data (API responses, user input, file data) matches expected types.
3. **Assert internal assumptions** — Use type guards or assertion functions to narrow types after validation.
4. **Reject unknown properties** — Strip or reject objects with properties not defined in the contract.
5. **Use branded types** — Distinguish semantically different primitives that share a runtime type.

## Contract Template

```typescript
type UserId = string & { readonly brand: unique symbol };
type OrderId = string & { readonly brand: unique symbol };

interface User {
  id: UserId;
  name: string;
  email: string;
  createdAt: Date;
}

function createUserId(id: string): UserId {
  if (!id || id.length < 8) throw new Error('Invalid UserId format');
  return id as UserId;
}

function isUser(val: unknown): val is User {
  return (
    typeof val === 'object' &&
    val !== null &&
    'id' in val && typeof (val as User).id === 'string' &&
    'name' in val && typeof (val as User).name === 'string'
  );
}
```

## Code Review Prompts

- Are all data contracts defined with explicit types rather than implicit assumptions?
- Does external data get validated against the expected contract before processing?
- Could two semantically different values (e.g., a UserId and an OrderId) be confused due to shared underlying type?

## Skill Deepening Notes

Mastering 'Validate Data Types and Contracts' requires understanding that TypeScript's type system, while powerful, only provides compile-time safety. Runtime data—from networks, user input, databases, and files—arrives as untyped JSON that may not conform to your assumptions. Validating the contract at runtime ensures that type errors are caught at the boundary, not deep in business logic where they are harder to diagnose. Types and contracts are not documentation—they are enforced constraints that prevent entire classes of bugs.
