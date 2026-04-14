---
name: test-integration-between-components
description: Write tests verifying that multiple components work together correctly. Test communication between modules, data flow between systems, and integration points. Include tests that go through multiple layers.
---
# Test Integration Between Components


# Overview

Write tests verifying that multiple components work together correctly. Test communication between modules, data flow between systems, and integration points. Include tests that go through multiple layers.

## When to Apply

- Multiple components working together
- Database integration
- API integration
- External service integration
- UI and logic interaction
- End-to-end workflows

## Examples

### ✓ Correct - Integration Testing
```javascript
describe('User registration workflow', () => {
    // Integration: Form → Service → Database
    test('complete registration flow works end-to-end', async () => {
        // User fills form
        const form = {
            name: 'John Doe',
            email: 'john@example.com',
            password: 'SecurePass123!'
        };
        
        // Service validates and saves
        const user = await UserService.register(form);
        
        // Database retrieval confirms save
        const savedUser = await database.getUser(user.id);
        
        expect(savedUser.name).toBe('John Doe');
        expect(savedUser.email).toBe('john@example.com');
    });
    
    // Integration: User creation triggers side effects
    test('user registration triggers email and notifications', async () => {
        const emailSpy = jest.spyOn(emailService, 'send');
        const notificationSpy = jest.spyOn(notificationService, 'create');
        
        const user = await UserService.register({
            name: 'John',
            email: 'john@example.com',
            password: 'SecurePass123!'
        });
        
        expect(emailSpy).toHaveBeenCalledWith({
            to: 'john@example.com',
            subject: 'Welcome to our service'
        });
        
        expect(notificationSpy).toHaveBeenCalledWith({
            userId: user.id,
            type: 'welcome'
        });
    });
    
    // Integration: Multiple services working together
    test('transfer between accounts updates both accounts and audit log', async () => {
        const fromAccount = await accountService.create({ balance: 1000 });
        const toAccount = await accountService.create({ balance: 500 });
        
        const auditSpy = jest.spyOn(auditLog, 'record');
        
        await transferService.transfer(fromAccount.id, toAccount.id, 100);
        
        expect(await accountService.getBalance(fromAccount.id)).toBe(900);
        expect(await accountService.getBalance(toAccount.id)).toBe(600);
        
        expect(auditSpy).toHaveBeenCalledWith({
            action: 'transfer',
            from: fromAccount.id,
            to: toAccount.id,
            amount: 100
        });
    });
});
```

### ❌ Incorrect - Missing Integration Testing
```javascript
describe('User registration', () => {
    test('validates form data', () => {
        expect(validateForm({ name: 'John', email: 'john@example.com' }))
            .toBe(true);
    });
    
    test('creates user object', () => {
        const user = createUserObject({ name: 'John' });
        expect(user.name).toBe('John');
    });
    // Tests individual components but never tests them together
});
```

## Rationale

Components can work individually but fail when integrated. Integration tests catch communication errors, data format mismatches, and workflow failures that unit tests miss.

## Verification

- Components interact correctly
- Data flows properly between layers
- Side effects trigger appropriately
- Error handling works end-to-end
- Complete workflows tested
## Common Failure Patterns

- Over-mocking, so tests pass but the real system fails when components connect.
- Not testing the data transformation that happens at component boundaries.
- Ignoring race conditions or timing dependencies between components.
- Testing that mocks were called rather than testing the actual integrated behavior.

## Test Design Framework

- **Contract Testing**: Verify that provider and consumer components agree on the data format exchanged.
- **Integration Matrix**: Map all interaction points between components and ensure each has a test.
- **Testcontainers**: Use real lightweight databases or services to eliminate mock brittleness.

## Expanded Test Checklist

1. Identify all components that must interact for each user story.
2. Replace unit-level mocks with real lightweight dependencies where feasible.
3. Execute the full user story through all component boundaries.
4. Verify end-to-end outcomes: data saved to DB, events emitted, responses returned.
5. Test failure propagation across component boundaries—what happens when one component fails?

## Advanced Example: Full-Path Testing

```typescript
describe('Order fulfillment - component integration', () => {
  test('creates order, decrements stock, and sends confirmation email', async () => {
    // Real database, real email mock
    const order = await OrderService.create({ productId: 1, quantity: 2, customerEmail: 'test@example.com' });
    
    const stock = await StockService.getAvailable(1);
    expect(stock).toBe(originalStock - 2);  // Stock decremented
    
    const savedOrder = await database.getOrder(order.id);
    expect(savedOrder.status).toBe('confirmed');
    
    expect(emailService.send).toHaveBeenCalledWith(
      'test@example.com',
      'Order Confirmed',
      expect.stringContaining(order.id)
    );
  });
});
```

## Code Review Prompts

- Do tests verify real component interactions, or just that mocks were called?
- Is the data transformation at each boundary verified with real data?
- Are failure paths tested across component boundaries to ensure errors propagate correctly?

## Skill Deepening Notes

Mastering 'Test Integration Between Components' requires understanding that components that work perfectly in isolation can fail spectacularly when integrated. Data format mismatches, missing error propagation, incorrect assumptions about what a downstream component provides—these are the bugs that escape unit testing. Integration tests are the bridge between "the code is correct" and "the system works."
