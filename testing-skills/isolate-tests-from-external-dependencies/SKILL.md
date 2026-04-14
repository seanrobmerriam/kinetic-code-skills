---
name: isolate-tests-from-external-dependencies
description: Tests must not depend on external systems, databases, or network calls. Mock external dependencies. Use test data, not production data. Tests must be independent and repeatable. Running tests multiple times must produce same results.
---
# Isolate Tests From External Dependencies


# Overview

Tests must not depend on external systems, databases, or network calls. Mock external dependencies. Use test data, not production data. Tests must be independent and repeatable. Running tests multiple times must produce same results.

## When to Apply

- Database calls
- Network requests
- External APIs
- File system operations
- System time/date
- Random number generation

## Examples

### ✓ Correct - Isolated Tests
```javascript
describe('User service', () => {
    let mockDatabase;
    let mockEmailService;
    
    beforeEach(() => {
        // Mock external dependencies
        mockDatabase = {
            saveUser: jest.fn(),
            getUser: jest.fn(),
            userExists: jest.fn()
        };
        
        mockEmailService = {
            sendWelcomeEmail: jest.fn()
        };
        
        // Inject mocks
        userService = new UserService(mockDatabase, mockEmailService);
    });
    
    test('registers user without calling real database', () => {
        mockDatabase.userExists.mockReturnValue(false);
        mockDatabase.saveUser.mockReturnValue({ id: 1 });
        
        userService.register({
            name: 'John',
            email: 'john@example.com'
        });
        
        // Verify mock was called correctly
        expect(mockDatabase.saveUser).toHaveBeenCalled();
        // No actual database involved
    });
    
    test('sends welcome email after registration', () => {
        mockDatabase.saveUser.mockReturnValue({ id: 1, email: 'john@example.com' });
        
        userService.register({
            name: 'John',
            email: 'john@example.com'
        });
        
        // Verify email was "sent" (mock called)
        expect(mockEmailService.sendWelcomeEmail).toHaveBeenCalledWith('john@example.com');
        // No actual email sent
    });
    
    test('handles date correctly with fixed time', () => {
        const fixedDate = new Date('2024-01-01T00:00:00Z');
        jest.spyOn(Date, 'now').mockReturnValue(fixedDate.getTime());
        
        const user = userService.register({ name: 'John', email: 'john@example.com' });
        
        expect(user.registeredAt).toEqual(fixedDate);
        
        Date.now.mockRestore();
    });
});
```

### ❌ Incorrect - Tests Dependent on External Systems
```javascript
describe('User service', () => {
    test('registers user', () => {
        // Calls real database - test fails if database down
        const user = userService.register({
            name: 'John',
            email: `john-${Math.random()}@example.com`
        });
        
        // Calls real email service - slow test
        expect(emailSent).toBe(true);
        // Test dependent on external systems
    });
});
```

## Rationale

Tests depending on external systems are slow, flaky, and fragile. Mocking ensures tests run fast, reliably, and consistently. Tests should verify logic, not external systems.

## Verification

- No real database calls
- No real network requests
- No real file system operations
- No dependencies on current time/date
- Tests run fast and reliably
- Tests independent and repeatable





## Common Failure Patterns

- Calling real databases or network services, causing slow and flaky tests.
- Using production data in tests instead of generated test fixtures.
- Tests that depend on execution order because they share mutable state.
- Not mocking time-based functions, causing date/time tests to fail in the future.

## Test Design Framework

- **Mock Injection**: Pass mocks as dependencies rather than importing real implementations.
- **Fixture Factories**: Generate test data programmatically rather than using static production snapshots.
- **Time Mocking**: Use fake timers or time injection to control dates in tests.
- **In-Memory Implementations**: Use in-memory databases or mock servers for integration tests.

## Expanded Test Checklist

1. Identify all external dependencies (database, network, filesystem, time).
2. Replace calls to external systems with mock implementations or test doubles.
3. Ensure mocks return realistic but generated data—no hardcoded production values.
4. Reset state between tests using beforeEach/afterEach or shared fixtures.
5. Verify mocks were called with the expected arguments, not just that they returned values.

## Advanced Example: Full-Path Testing

```typescript
describe('UserService with mocked dependencies', () => {
  let mockDb;
  let mockEmailer;
  let userService;

  beforeEach(() => {
    mockDb = { find: jest.fn(), save: jest.fn() };
    mockEmailer = { send: jest.fn() };
    userService = new UserService(mockDb, mockEmailer);
  });

  test('sends welcome email after user creation', async () => {
    mockDb.save.mockReturnValue({ id: 1, email: 'test@example.com' });
    
    await userService.createUser({ name: 'Test', email: 'test@example.com' });

    expect(mockDb.save).toHaveBeenCalled();
    expect(mockEmailer.send).toHaveBeenCalledWith(
      'test@example.com',
      'Welcome',
      expect.any(String)
    );
  });

  test('does not call email when database save fails', async () => {
    mockDb.save.mockRejectedValue(new Error('DB error'));
    
    await expect(userService.createUser({ name: 'Test', email: 'test@example.com' }))
      .rejects.toThrow('DB error');
    
    expect(mockEmailer.send).not.toHaveBeenCalled();
  });
});
```

## Code Review Prompts

- Does this test call any real external system (database, network, filesystem)?
- Is test data generated or does it use production data snapshots?
- Does test order or previous test execution affect this test's outcome?

## Skill Deepening Notes

Mastering 'Isolate Tests From External Dependencies' requires understanding that tests are contracts with developers. A test that passes today should pass tomorrow, regardless of network connectivity, database state, or the current date. Tests that depend on external systems are not reliable—they can fail due to factors outside the code under test. Isolation ensures tests are fast, repeatable, and focused entirely on the logic being verified.
