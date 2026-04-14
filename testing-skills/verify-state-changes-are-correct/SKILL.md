---
name: verify-state-changes-are-correct
description: Test that code correctly modifies state. Verify both what changed and what didn't change. Test that state transitions are valid. Confirm that side effects are appropriate.
---
# Verify State Changes Are Correct


# Overview

Test that code correctly modifies state. Verify both what changed and what didn't change. Test that state transitions are valid. Confirm that side effects are appropriate.

## When to Apply

- Any state modification
- Database operations
- Object mutations
- Configuration changes
- User data updates
- Complex state machines

## Examples

### ✓ Correct - State Change Verification
```javascript
describe('User state changes', () => {
    test('update user name changes only name, not other fields', () => {
        const user = new User({
            id: 1,
            name: 'John',
            email: 'john@example.com',
            createdAt: new Date('2024-01-01')
        });
        
        user.updateName('Jane');
        
        // Verify what changed
        expect(user.name).toBe('Jane');
        
        // Verify what didn't change
        expect(user.id).toBe(1);
        expect(user.email).toBe('john@example.com');
        expect(user.createdAt).toEqual(new Date('2024-01-01'));
    });
    
    test('activate account changes status and sets activation date', () => {
        const user = new User({ status: 'inactive', activatedAt: null });
        
        user.activate();
        
        expect(user.status).toBe('active');
        expect(user.activatedAt).toBeDefined();
        expect(user.activatedAt).toBeCloseTo(new Date(), 1000);  // Within 1 second
    });
    
    test('state transition from pending to approved only when valid', () => {
        const order = new Order({ status: 'pending' });
        
        order.approve();
        expect(order.status).toBe('approved');
        expect(order.approvedAt).toBeDefined();
        
        // Invalid transition from approved
        expect(() => order.pendingAgain()).toThrow('Cannot transition from approved to pending');
    });
});

describe('Database operations', () => {
    test('update user persists changes', () => {
        const user = new User({ id: 1, name: 'John' });
        user.updateName('Jane');
        user.save();
        
        // Verify database has new value
        const savedUser = database.getUser(1);
        expect(savedUser.name).toBe('Jane');
    });
    
    test('delete removes item without affecting others', () => {
        database.addUser({ id: 1, name: 'User 1' });
        database.addUser({ id: 2, name: 'User 2' });
        database.addUser({ id: 3, name: 'User 3' });
        
        database.deleteUser(2);
        
        // User 2 deleted
        expect(database.getUser(2)).toBeNull();
        
        // Others unaffected
        expect(database.getUser(1)).toBeDefined();
        expect(database.getUser(3)).toBeDefined();
    });
});
```

### ❌ Incorrect - Missing State Verification
```javascript
describe('User state changes', () => {
    test('update user name', () => {
        const user = new User({ name: 'John' });
        user.updateName('Jane');
        
        expect(user.name).toBe('Jane');
        // Never verified other fields didn't change
        // Never verified side effects
    });
});
```

## Rationale

State changes are critical. Tests must verify not just what changed, but that nothing else changed unexpectedly. This catches side effects and ensures operations are safe.

## Verification

- Target state changed correctly
- Unrelated state unchanged
- Side effects appropriate
- Invalid state transitions rejected
- State consistency verified





## Common Failure Patterns

- Asserting only the changed values without checking that unrelated state remained unchanged.
- Modifying shared state in tests without resetting it, causing test interdependencies.
- Not verifying that side effects (database writes, events emitted, logs written) occurred.
- Testing state mutations without checking intermediate states during the transition.

## Test Design Framework

- **State Diff Tests**: After an operation, explicitly assert what changed AND what did not change.
- **Snapshot Testing**: Capture a complete state snapshot before and after, then diff them.
- **Side Effect Verification**: Assert that emails were sent, events were emitted, or logs were written as expected.

## Expanded Test Checklist

1. Identify all state that should change as a result of the operation.
2. Identify all state that should NOT change (the complement set).
3. Capture state before the operation for both changed and unchanged entities.
4. Execute the operation and verify changed state matches expectations.
5. Verify unchanged state is byte-for-byte identical to its pre-operation value.

## Advanced Example: Full-Path Testing

```typescript
describe('User.updateProfile() - state changes', () => {
  test('updates only the name, leaving email and id unchanged', () => {
    const user = new User({ id: 1, name: 'John', email: 'john@example.com', createdAt: new Date('2024-01-01') });
    const originalEmail = user.email;
    const originalCreatedAt = user.createdAt;

    user.updateProfile({ name: 'Jane' });

    expect(user.name).toBe('Jane');           // Changed
    expect(user.email).toBe(originalEmail);   // Unchanged
    expect(user.id).toBe(1);                  // Unchanged
    expect(user.createdAt).toEqual(originalCreatedAt); // Unchanged
  });

  test('activate() sets status and activationDate, nothing else', () => {
    const user = new User({ status: 'inactive', activatedAt: null });
    user.activate();
    expect(user.status).toBe('active');
    expect(user.activatedAt).toBeDefined();
  });
});
```

## Code Review Prompts

- After this operation, can you enumerate exactly what changed and what didn't?
- Are side effects (events, logs, emails) verified as part of state change tests?
- Does the test reset shared state to avoid polluting subsequent tests?

## Skill Deepening Notes

Mastering 'Verify State Changes Are Correct' requires understanding that state is the memory of your system. When state changes incorrectly, the effects are often silent and cumulative—a user appears to have two balances, an order ships twice, or a permission persists after revocation. Verifying state changes means being explicit about the before and after, ensuring not just that the new value is correct but that no unintended side effects occurred.
