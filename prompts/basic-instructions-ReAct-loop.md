# CLAUDE.md

## Context
You are working on this codebase as a senior engineer. A human developer owns every change and reviews it, so keep your work transparent and easy to trace: small diffs, clear reasoning, tests that prove the change works.

## Commands
<!-- TODO: replace the placeholders with this project's real commands -->
- Install: `pnpm install`
- Run app: `pnpm dev`
- Test (run after every change): `pnpm test`
- Lint & format: `pnpm lint`

## Rule priority
When two rules pull in different directions, simplicity and minimal change win. The principles below take precedence over the style rules further down.

## Core principles (anti-overengineering)
- **KISS & YAGNI:** Implement only what was asked for now. Do not create abstractions, factories, wrappers or generic hooks for "future use".
- **Minimal edits:** Change as few lines and files as possible. Do not refactor adjacent code that isn't broken.
- **No extra files:** Add functions to existing files rather than creating new ones, unless asked or unless a file would grow past ~500 lines.
- **No new dependencies:** Ask for explicit approval before installing any package.
- **Direct code:** Prefer explicit, easy-to-read code over clever or heavily abstracted solutions.

## Workflow
1. **Understand:** Map which files and entities the task touches before editing.
2. **Test first:** Write a failing unit or regression test that captures the requirement or bug (see the TDD flow under Testing).
3. **Implement:** Write the minimal code that makes it pass.
4. **Tidy:** Clean up only the code you wrote or changed in this task.
5. **Verify:** Run the test suite and lint. Report the result.

- Before editing, state your plan in a few lines: what you'll change and why.
- For multi-step tasks, use a TODO checklist and mark an item done only after its tests pass.
- For structural refactors, briefly compare two approaches before choosing one. If the chosen one breaks existing tests and the fix isn't trivial, revert and try the other.

## Stop and ask the developer when
- Tests fail 3 times in a row on the same fix.
- The task requires an unplanned change to a database schema or public API contract.
- A new third-party library seems necessary.

When you stop, list what is done, what is pending, and what is blocking you.

## Code style
- **Functions:** Aim for 20 lines or fewer, one responsibility each.
- **Naming:** Specific, descriptive names. Avoid generic names like `data`, `handler`, `Manager`, `processor`.
- **Typing:** Strict and explicit. No `any`, no untyped functions, no loose `Record<string, any>`/`Dict`.
- **Control flow:** Early returns instead of nested `if`s (max 2 levels of indentation). Extract logic only when it is actually duplicated.
- **Errors:** Exception messages include the received value and what was expected.
- **Formatting:** Leave it to the formatter (`prettier`/`black`/etc.). Don't hand-format.
- **Logging:** Structured JSON for debug/observability logs; plain text only for user-facing CLI output.

## Comments & docs
- Never remove existing comments when refactoring; they carry intent and history.
- Comment the WHY, not the WHAT.
- Public functions get a docstring with their intent and one usage example.
- When a line exists because of a specific bug or upstream constraint, reference the issue number or commit SHA.

## Testing
### TDD flow
1. Write the tests first, from the expected inputs and outputs. Do not write implementation code in this step, not even stubs beyond what is needed to compile.
2. Run the tests and confirm they fail for the expected reason (not a syntax or import error).
3. For non-trivial features, stop and show the tests to the developer for review before implementing. For small, well-defined changes, continue directly.
4. Implement the minimal code to make them pass, then iterate: run tests, fix, repeat.
5. Run the full suite to make sure nothing else broke.

### Rules
- Never modify, weaken or delete existing tests to make them pass. If a test seems wrong, stop and ask.
- Never special-case the implementation for specific test inputs; solve the general requirement.
- Each test checks behavior (what the code should do), not implementation details.
- Every new function gets a test; every bug fix gets a regression test that fails before the fix.
- Tests must be fast, independent, repeatable and self-validating.
- Replace external I/O (APIs, DB, filesystem) with named fake classes in the project, not inline stubs.

## Architecture
- Inject dependencies through constructors or parameters; core modules don't import infrastructure directly or use global instances.
- Wrap a third-party library behind a thin project-owned interface only when core code depends on it and tests need to fake it. Don't wrap anything else.
- Follow the framework's own conventions (e.g. NestJS modules/providers).

## Example of the expected style
```typescript
/**
 * Applies a renewal fee to the customer's account balance.
 *
 * @example
 * applyRenewalFee(100.0, 15.0) // 85.0
 */
export function applyRenewalFee(currentBalance: number, feeAmount: number): number {
  if (feeAmount <= 0) {
    throw new InvalidArgumentException(
      `Fee amount must be positive. Received: ${feeAmount} (expected: > 0)`,
    );
  }

  if (currentBalance < feeAmount) {
    throw new InsufficientBalanceException(
      `Insufficient balance. Received balance: ${currentBalance} (expected: >= fee ${feeAmount})`,
    );
  }

  return currentBalance - feeAmount;
}

describe('applyRenewalFee', () => {
  it('subtracts the fee when the balance is sufficient', () => {
    expect(applyRenewalFee(100.0, 15.0)).toBe(85.0);
  });
});
```