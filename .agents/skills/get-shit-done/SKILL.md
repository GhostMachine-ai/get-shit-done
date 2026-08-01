```markdown
# get-shit-done Development Patterns

> Auto-generated skill from repository analysis

## Overview
This skill teaches the core development patterns and conventions used in the `get-shit-done` TypeScript codebase. You'll learn how to structure files, write imports and exports, follow commit message patterns, and organize tests. While no specific framework is used, the repository maintains clear and consistent standards for maintainability and collaboration.

## Coding Conventions

### File Naming
- Use **kebab-case** for all file names.
  - Example:  
    ```
    task-manager.ts
    utils/helpers.ts
    ```

### Import Style
- Use **relative imports** for referencing other modules.
  - Example:
    ```typescript
    import { addTask } from './task-manager';
    import { formatDate } from '../utils/helpers';
    ```

### Export Style
- Prefer **named exports** over default exports.
  - Example:
    ```typescript
    // In task-manager.ts
    export function addTask(task: Task) { ... }
    export function removeTask(id: string) { ... }
    ```

### Commit Messages
- Freeform style, sometimes with prefixes.
- Average commit message length: ~64 characters.
  - Example:
    ```
    Add initial implementation of task scheduler
    Fix bug in task removal logic
    ```

## Workflows

### Adding a New Feature
**Trigger:** When you want to introduce a new capability or module.  
**Command:** `/add-feature`

1. Create a new file using kebab-case (e.g., `feature-name.ts`).
2. Implement the feature with named exports.
3. Use relative imports to integrate with existing modules.
4. Write or update corresponding test files (`feature-name.test.ts`).
5. Commit changes with a clear, descriptive message.

### Fixing a Bug
**Trigger:** When you need to resolve a defect in the codebase.  
**Command:** `/fix-bug`

1. Locate the relevant file(s) using kebab-case naming.
2. Apply the fix, maintaining import/export conventions.
3. Update or add tests in `*.test.ts` files to cover the bug.
4. Commit with a message describing the fix.

### Writing and Running Tests
**Trigger:** When adding new code or verifying existing functionality.  
**Command:** `/run-tests`

1. Create or update test files matching the pattern `*.test.ts`.
2. Write tests for all exported functions and modules.
3. Use the project's preferred test runner (framework unknown; see project docs or `package.json`).
4. Run the tests and ensure all pass before committing.

## Testing Patterns

- Test files are named with the pattern `*.test.ts`.
- Place tests alongside the modules they cover or in a dedicated test directory.
- Each exported function or feature should have corresponding tests.
- Example test file structure:
  ```typescript
  // task-manager.test.ts
  import { addTask, removeTask } from './task-manager';

  describe('Task Manager', () => {
    it('should add a task', () => {
      // test implementation
    });

    it('should remove a task', () => {
      // test implementation
    });
  });
  ```

## Commands
| Command       | Purpose                                      |
|---------------|----------------------------------------------|
| /add-feature  | Start the workflow for adding a new feature  |
| /fix-bug      | Start the workflow for fixing a bug          |
| /run-tests    | Run all test files in the codebase           |
```
