---
name: use-clear-exit-conditions-for-loops
description: Every loop must have a clear, explicit exit condition. Infinite loops are forbidden unless absolutely necessary and documented. Loop variables must be initialized, modified, and checked correctly. Don't modify loop variables within the loop body.
---
# Use Clear Exit Conditions for Loops


# Overview

Every loop must have a clear, explicit exit condition. Infinite loops are forbidden unless absolutely necessary and documented. Loop variables must be initialized, modified, and checked correctly. Don't modify loop variables within the loop body.

## When to Apply

- for, while, do-while loops
- Recursive functions
- Event listeners
- Long-running processes
- Any unbounded repetition

## Examples

### ✓ Correct - Clear Exit Conditions
```javascript
// For loop: clear bounds
for (let i = 0; i < items.length; i++) {
    console.log(items[i]);
}

// While loop: clear condition
let count = 0;
while (count < maxRetries) {
    const result = attemptOperation();
    if (result.success) break;  // Explicit exit
    count++;  // Modify outside condition logic
}

// Recursive: clear termination
function countdown(n) {
    if (n <= 0) return;  // Base case: exit condition
    console.log(n);
    countdown(n - 1);  // Recursive: progress toward base case
}

// Intentional infinite loop: well documented
// Continuous server that processes messages indefinitely
while (true) {
    const message = messageQueue.wait();
    processMessage(message);
    // This loop intentionally runs forever until process is killed
}
```

### ❌ Incorrect - Unclear Exit Conditions
```javascript
// Infinite loop: no exit condition
for (let i = 0; i < items.length; ) {
    console.log(items[i]);
    // Missing i++ - infinite loop!
}

// Unclear loop variable modification
let count = 0;
while (count < 10) {
    result = operation();
    if (result) count = 20;  // Modifying in body: confusing
    count++;
}

// Recursive: no base case
function countdown(n) {
    console.log(n);
    countdown(n - 1);  // Infinite recursion! Stack overflow
}
```

## Rationale

Loop errors cause hangs and infinite recursion. Clear exit conditions make behavior predictable and prevent resource exhaustion.

## Verification

- All loops have explicit exit conditions
- Loop variables initialized, modified, checked correctly
- Recursive functions have base cases
- No infinite loops without documentation
- Loop exit conditions tested





## Common Failure Patterns

- Modifying the loop counter inside the loop body, making the exit condition impossible to reason about.
- Using floating-point numbers as loop counters, leading to infinite loops due to precision errors.
- Recursive functions without a guaranteed base case that progresses toward termination.
- While loops with conditions that never become false due to off-by-one errors.

## Design Patterns to Apply

- **Fixed-Boundary Loops**: Prefer `for(let i = 0; i < n; i++)` over while loops when the iteration count is determinable.
- **Recursion with Trampolines**: For recursive functions that might blow the stack, use trampolining or iterative alternatives.
- **Early Break Guards**: Use `break` and `continue` sparingly and only when they make the exit intent obvious.
- **Iterator Protocol**: Prefer iterators and `for...of` over manual index management to avoid bounds errors.

## Implementation Playbook

1. **Verify initialization** — Confirm loop variables are initialized to correct starting values.
2. **Check termination** — Ensure the exit condition will eventually become true given the loop body modifications.
3. **Validate modification** — Confirm loop variables are modified correctly (increment/decrement) and only in the loop header or known-safe locations.
4. **Test edge cases** — Run with 0 iterations, 1 iteration, and maximum expected iterations to verify correct behavior.
5. **Audit recursion** — Every recursive function needs a base case that is guaranteed to be reached and a measure that decreases toward it.

## Contract Template

```typescript
// Iterative binary search with clear exit conditions
function binarySearch<T>(sortedArray: T[], target: T, comparator: (a: T, b: T) => number): number {
  let left = 0;
  let right = sortedArray.length - 1;

  while (left <= right) {  // Clear: terminates when left > right
    const mid = Math.floor((left + right) / 2);
    const cmp = comparator(sortedArray[mid], target);

    if (cmp === 0) return mid;       // Found: exit with index
    if (cmp < 0) left = mid + 1;    // Target is right half
    else right = mid - 1;            // Target is left half
  }

  return -1;  // Not found: exit with sentinel
}

// Recursive with guaranteed termination
function factorial(n: number): number {
  if (n < 0) throw new Error('Factorial undefined for negative numbers');
  if (n <= 1) return 1;  // Base case: terminates here
  return n * factorial(n - 1);  // Progress: n decreases toward base case
}
```

## Code Review Prompts

- Is it possible to determine the maximum number of iterations this loop will execute before running it?
- Could any modification to loop variables cause the exit condition to never be met?
- For recursive functions, is there a guarantee that the recursion depth is bounded?

## Skill Deepening Notes

Mastering 'Use Clear Exit Conditions for Loops' requires understanding that loops are one of the most common sources of hangs, freezes, and stack overflows. An infinite loop consumes CPU forever; infinite recursion eventually crashes with a stack overflow. Clear exit conditions mean the termination condition is visible in one place, the loop variable progresses monotonically toward the exit, and the loop body cannot circumvent the exit condition. When loops become complex, prefer extracting them into named functions with documented termination conditions.
