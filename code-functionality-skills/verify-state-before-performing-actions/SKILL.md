---
name: verify-state-before-performing-actions
description: Before performing important actions (delete, modify, publish), verify the current state and that all preconditions are met. Check permissions, validate state transitions, confirm no conflicts exist. Document what state is expected.
---
# Verify State Before Performing Actions


# Overview

Before performing important actions (delete, modify, publish), verify the current state and that all preconditions are met. Check permissions, validate state transitions, confirm no conflicts exist. Document what state is expected.

## When to Apply

- Delete operations
- State changes
- Authorization checks
- Permission validation
- Workflow transitions
- Resource modifications

## Examples

### ✓ Correct - State Verification
```javascript
async function deleteUser(userId, requestingUser) {
    // Verify user exists
    const user = await database.getUser(userId);
    if (!user) {
        throw new Error(`User ${userId} not found`);
    }
    
    // Verify permission
    if (!requestingUser.canDeleteUsers) {
        throw new Error('Insufficient permissions to delete users');
    }
    
    // Verify not self-deletion
    if (userId === requestingUser.id) {
        throw new Error('Users cannot delete themselves');
    }
    
    // Verify no dependent records
    const dependents = await database.findDependentRecords(userId);
    if (dependents.length > 0) {
        throw new Error(`Cannot delete user with ${dependents.length} dependent records`);
    }
    
    // All preconditions met: safe to delete
    await database.deleteUser(userId);
}

async function publishPost(postId, user) {
    const post = await database.getPost(postId);
    
    // Verify state
    if (post.status !== 'draft') {
        throw new Error(`Cannot publish ${post.status} post`);
    }
    
    // Verify ownership
    if (post.authorId !== user.id) {
        throw new Error('Only post author can publish');
    }
    
    // Verify content valid
    if (!post.title || !post.content) {
        throw new Error('Post must have title and content');
    }
    
    // State verified: safe to publish
    post.status = 'published';
    post.publishedAt = new Date();
    await database.save(post);
}
```

### ❌ Incorrect - No State Verification
```javascript
async function deleteUser(userId) {
    // No verification - assumes deletion is safe
    await database.deleteUser(userId);
    // What if user doesn't exist? What if user has dependencies?
}

async function publishPost(postId) {
    // No verification - assumes safe to publish
    const post = await database.getPost(postId);
    post.status = 'published';  // Could publish empty post, non-draft post, etc.
    await database.save(post);
}
```

## Rationale

Destructive actions must be careful. State verification prevents invalid operations, data loss, and security vulnerabilities. It makes operations safe and predictable.

## Verification

- All destructive operations verify preconditions
- State is as expected before action
- Permissions/authorization checked
- No conflicting operations possible
- Edge cases tested





## Common Failure Patterns

- Deleting a resource without checking if it exists first, resulting in spurious errors.
- Performing state transitions without verifying the current state allows invalid transitions (e.g., publishing an already-published post).
- Modifying shared data without checking for concurrent modifications, leading to lost updates.
- Trusting client-supplied IDs or references without verifying they point to valid, owned resources.

## Design Patterns to Apply

- **Precondition Assertions**: Assert expected state before mutations; fail fast if assumptions are violated.
- **Optimistic Locking**: Include a version field in state and reject updates if the version has changed since read.
- **State Machine**: Model discrete states explicitly and validate transitions are allowed before executing them.
- **Authorization Matrix**: Cross-check caller permissions against the target resource's ownership or access control list.

## Implementation Playbook

1. **Identify preconditions** — For each action, list what must be true before it executes (resource exists, user has permission, state is valid).
2. **Check in order** — Verify preconditions in a logical order, failing fast on the first violation.
3. **Provide clear errors** — Each precondition failure should produce a specific, actionable error message.
4. **Handle race conditions** — Use locking, versioning, or atomic compare-and-swap to prevent TOCTOU (time-of-check-time-of-use) bugs.
5. **Document assumptions** — Add comments explaining what state is expected and why.

## Contract Template

```typescript
async function publishPost(postId: string, userId: string): Promise<Post> {
  const post = await db.posts.find(postId);

  if (!post) throw new NotFoundError(`Post ${postId} not found`);
  if (post.status === 'published') throw new InvalidStateError('Post is already published');
  if (post.authorId !== userId) throw new ForbiddenError('Only the author can publish');
  if (!post.title || !post.content) throw new ValidationError('Post must have title and content before publishing');

  // All preconditions met
  post.status = 'published';
  post.publishedAt = new Date();
  post.version++;  // Optimistic locking

  const updated = await db.posts.update(postId, post, { expectedVersion: post.version - 1 });
  if (!updated) throw new ConflictError('Post was modified by another request');

  return updated;
}
```

## Code Review Prompts

- What preconditions must be true before this action executes, and are they all checked?
- Could another concurrent operation change the state between the check and the action?
- Is the error returned when a precondition fails specific enough to understand what went wrong?

## Skill Deepening Notes

Mastering 'Verify State Before Performing Actions' requires understanding that destructive operations are irreversible and must be treated with caution. The question "is it safe to do this?" must be answered by inspecting the actual current state—not assumptions, not cached values, not client-supplied facts. State verification is the checkpoint before committed changes. Skipping it leads to corrupted data, security vulnerabilities, and unpredictable system behavior.
