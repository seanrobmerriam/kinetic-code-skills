---
name: maintain-consistent-state-throughout-operations
description: Code must maintain consistent state before, during, and after operations. Partial updates that leave system in inconsistent state are forbidden. Use transactions or atomic operations. If operation fails, restore previous state.
---
# Maintain Consistent State Throughout Operations


# Overview

Code must maintain consistent state before, during, and after operations. Partial updates that leave system in inconsistent state are forbidden. Use transactions or atomic operations. If operation fails, restore previous state.

## When to Apply

- Multi-step operations
- Database updates
- File modifications
- User authentication/authorization
- Configuration changes
- Data migrations

## Examples

### ✓ Correct - Consistent State
```javascript
async function transferFunds(fromAccount, toAccount, amount) {
    // Create backup of original state
    const originalFromBalance = fromAccount.balance;
    const originalToBalance = toAccount.balance;
    
    try {
        // Step 1: Debit from source
        fromAccount.balance -= amount;
        await database.save(fromAccount);
        
        // Step 2: Credit to destination
        toAccount.balance += amount;
        await database.save(toAccount);
        
        // Both succeeded: operation complete, state is consistent
        return { success: true, fromAccount, toAccount };
    } catch (error) {
        // Restore original state if any step failed
        fromAccount.balance = originalFromBalance;
        toAccount.balance = originalToBalance;
        
        console.error('Transfer failed, balances restored');
        throw error;
    }
}
```

### ❌ Incorrect - Inconsistent State
```javascript
async function transferFunds(fromAccount, toAccount, amount) {
    // Step 1: Debit from source
    fromAccount.balance -= amount;
    await database.save(fromAccount);
    
    // Step 2: Credit to destination
    toAccount.balance += amount;
    await database.save(toAccount);  // What if this fails?
    
    // If second save fails, money disappeared!
    // State is now inconsistent
}
```

## Rationale

Inconsistent state causes data corruption, lost transactions, and difficult bugs. Atomic operations ensure either full success or full rollback - never partial states.

## Verification

- All state changes are atomic or transactional
- Rollback procedures tested
- Partial failure states impossible
- State consistency verified after operations
- Transaction boundaries clear





## Common Failure Patterns

- Updating multiple records in a loop without transactional protection—partial updates leave data inconsistent.
- Modifying shared state without acquiring locks, leading to race conditions.
- Failing to rollback on error, leaving the system in a half-completed state.
- Not validating preconditions before beginning a state-changing operation.

## Design Patterns to Apply

- **Transaction Pattern**: Wrap multi-step updates in a transaction that commits only if all steps succeed.
- **Saga Pattern**: For distributed systems, chain local transactions with compensating rollback actions.
- **Immutable Updates**: Never mutate existing state; create new state objects that represent the complete post-operation snapshot.
- **Two-Phase Commit**: For critical operations, prepare all changes then commit atomically.

## Implementation Playbook

1. **Identify stateful operations** — List every operation that modifies data across multiple entities or systems.
2. **Define transaction boundaries** — Decide what constitutes a single atomic unit of work.
3. **Capture pre-state** — Before modifying, record the original state to enable rollback.
4. **Implement rollback** — In case of any failure, restore the captured pre-state.
5. **Verify post-conditions** — After successful completion, confirm the new state is internally consistent.

## Contract Template

```typescript
async function transferFunds(
  from: Account,
  to: Account,
  amount: number
): Promise<{ success: boolean; error?: string }> {
  const snapshot = { from: from.balance, to: to.balance };

  try {
    from.balance -= amount;
    if (from.balance < 0) throw new InsufficientFundsError();
    await db.save(from);

    to.balance += amount;
    await db.save(to);

    return { success: true };
  } catch (error) {
    // Compensating transaction: restore balances
    from.balance = snapshot.from;
    to.balance = snapshot.to;
    await db.save(from);
    await db.save(to);
    return { success: false, error: error.message };
  }
}
```

## Code Review Prompts

- If this operation fails halfway through, will the system be left in a valid state?
- Are there any concurrent operations that could read inconsistent intermediate state?
- Is there a rollback mechanism for every state-changing operation?

## Skill Deepening Notes

Mastering 'Maintain Consistent State Throughout Operations' requires understanding that data integrity is the foundation of a reliable system. An operation that debits one account but fails to credit another creates money from nothing—a catastrophic failure. Consistent state means that no matter when you observe the system (during an operation, after a failure, or after recovery), the data tells a coherent story. Partial updates are almost never acceptable; either the entire operation completes, or the system returns to its exact prior state.
