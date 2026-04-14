---
name: provide-clear-feedback-on-operation-results
description: Every operation must provide clear feedback on success, failure, or partial completion. Users must know what happened, why it happened, and what to do next. Log operations for debugging. Return structured results with status and details.
---
# Provide Clear Feedback on Operation Results


# Overview

Every operation must provide clear feedback on success, failure, or partial completion. Users must know what happened, why it happened, and what to do next. Log operations for debugging. Return structured results with status and details.

## When to Apply

- User actions
- External operations
- Long-running tasks
- System state changes
- API responses
- Batch operations

## Examples

### ✓ Correct - Clear Feedback
```javascript
async function uploadFiles(files) {
    const results = {
        successful: [],
        failed: [],
        skipped: [],
        errors: []
    };
    
    for (const file of files) {
        try {
            // Validate before upload
            if (file.size > MAX_SIZE) {
                results.skipped.push({
                    filename: file.name,
                    reason: `File exceeds max size of ${MAX_SIZE} bytes`
                });
                continue;
            }
            
            // Attempt upload
            const uploadResult = await uploadToServer(file);
            results.successful.push({
                filename: file.name,
                url: uploadResult.url,
                size: file.size
            });
            
            // Log success with context
            console.info(`Uploaded: ${file.name} (${file.size} bytes)`);
        } catch (error) {
            results.failed.push({
                filename: file.name,
                error: error.message
            });
            
            // Log failure with context
            console.error(`Upload failed: ${file.name} - ${error.message}`);
        }
    }
    
    return {
        status: results.failed.length === 0 ? 'success' : 'partial',
        totalProcessed: files.length,
        summary: {
            successful: results.successful.length,
            failed: results.failed.length,
            skipped: results.skipped.length
        },
        details: results
    };
}
```

### ❌ Incorrect - No Clear Feedback
```javascript
async function uploadFiles(files) {
    // No feedback on what happened
    for (const file of files) {
        await uploadToServer(file);
    }
    // Caller doesn't know what succeeded, failed, or why
}
```

## Rationale

Users and operators need to know what happened. Clear feedback enables informed decisions and troubleshooting. It improves user experience and system reliability.

## Verification

- Success is clearly reported
- Failures are clearly reported with reasons
- Operation status is unambiguous
- Results are structured and detailed
- Logging provides debugging context





## Common Failure Patterns

- Returning `null` or `undefined` to indicate failure instead of a structured result, forcing callers to guess.
- Using boolean return types (`true`/`false`) that cannot explain why an operation failed.
- Logging at incompatible levels—errors not logged, or debug info logged as errors.
- Returning different result structures from different code paths, forcing complex caller-side parsing.

## Design Patterns to Apply

- **Result Object Pattern**: Return `{ ok: boolean; data?: T; error?: E }` or discriminated unions.
- **Structured Error Types**: Define error classes/codes that carry context (what failed, why, what to do).
- **Operation Summary**: For batch operations, return a summary object with counts of successes, failures, and skipped.
- **Log Levels by Outcome**: Use `info` for success, `warn` for recoverable failures, `error` for system failures.

## Implementation Playbook

1. **Standardize result types** — Choose Result objects or thrown exceptions for your codebase; don't mix approaches.
2. **Include context in failures** — Every error should state what failed, what input was received, and what was expected.
3. **Return partial results for batch ops** — When processing multiple items, return which succeeded, which failed, and why.
4. **Log at action boundaries** — Log when operations begin, when they complete, and when they fail—with correlation IDs.
5. **Document result shapes** — Make the structure of success and failure returns explicit in function signatures.

## Contract Template

```typescript
interface OperationResult<T, E = string> {
  ok: boolean;
  data?: T;
  error?: E;
  metadata?: {
    durationMs: number;
    timestamp: Date;
  };
}

async function processOrder(orderId: string): Promise<OperationResult<Order>> {
  const start = Date.now();
  try {
    const order = await db.orders.find(orderId);
    if (!order) {
      return { ok: false, error: `Order ${orderId} not found`, metadata: { durationMs: Date.now() - start, timestamp: new Date() } };
    }
    if (order.status !== 'pending') {
      return { ok: false, error: `Cannot process order in ${order.status} status`, metadata: { durationMs: Date.now() - start, timestamp: new Date() } };
    }
    const processed = await billing.charge(order);
    order.status = 'processed';
    await db.orders.save(order);
    return { ok: true, data: order, metadata: { durationMs: Date.now() - start, timestamp: new Date() } };
  } catch (err) {
    return { ok: false, error: `Order processing failed: ${err.message}`, metadata: { durationMs: Date.now() - start, timestamp: new Date() } };
  }
}
```

## Code Review Prompts

- Can a caller determine the exact outcome of this operation without guessing or inspecting exceptions?
- Does the error return value provide enough context to understand what went wrong and how to fix it?
- Are batch or multi-step operations returning structured summaries rather than only reporting the first failure?

## Skill Deepening Notes

Mastering 'Provide Clear Feedback on Operation Results' requires understanding that systems do not exist in isolation—they are operated by humans and integrated with other systems. When something goes wrong, the difference between a clear error message and a cryptic null pointer exception is the difference between a five-minute fix and a five-hour debugging session. Feedback is not optional metadata—it is the primary interface between the system and everyone who must interact with it when things go wrong.
