---
name: bun-testing
description: Write, modify, debug, or review bun:test suites with deterministic isolation. Use for Bun test failures, module mocks, spies, globals, order-dependent behavior, and tests that pass alone but fail in the suite.
license: MIT
---

# Bun testing

Keep module identity stable. Change mock behavior instead of repeatedly replacing the module graph.

Treat `mock.module()` overrides as lasting for the lifetime of the JavaScript global that loaded the test file. `mock.restore()` and `mock.clearAllMocks()` do not remove them. With per-file isolation that lifetime aligns with one file; without it, an override can be visible to later files. When behavior depends on test order, or a test passes alone but fails in the suite, investigate module mocks, import timing, and shared state first.

## Choose the narrowest mock

Prefer these boundaries, in order:

1. Inject a dependency.
2. Use `spyOn` for a replaceable method or exported function.
3. Pass a standalone `mock()` function or object.
4. Register one file-scoped `mock.module()` and vary stable mock functions.
5. Split tests into files when they require different module graphs.

Mock external, stateful, expensive, or nondeterministic dependencies. In integration tests, keep internal routing, validation, and service code real when practical, and control the external boundary instead.

Do not add dependency injection everywhere solely for tests. Consider it when a dependency is frequently mocked or its import-time behavior makes tests hard to isolate.

## Keep one module graph per file

Do not call `mock.module()` repeatedly for the same specifier in `test`, `it`, `beforeEach`, or per-test helpers. Resetting or restoring mocks does not recreate the original module graph.

Register a module mock once and retain stable mock functions:

```ts
import { beforeEach, describe, expect, mock, test } from "bun:test";

const getUserMock = mock();
const saveUserMock = mock();

mock.module("./user-repository", () => ({
  getUser: getUserMock,
  saveUser: saveUserMock,
}));

import { updateUser } from "./update-user";

describe("updateUser", () => {
  beforeEach(() => {
    getUserMock.mockReset();
    saveUserMock.mockReset();
  });

  test("updates an existing user", async () => {
    // Arrange
    getUserMock.mockResolvedValue({ id: "1", name: "Old" });
    saveUserMock.mockResolvedValue({ id: "1", name: "New" });

    // Act
    const result = await updateUser("1", { name: "New" });

    // Assert
    expect(result.name).toBe("New");
    expect(saveUserMock).toHaveBeenCalledTimes(1);
  });
});
```

Each test should configure behavior that matters to its result. Avoid broad default implementations that make tests depend silently on shared setup.

Split the file when test groups require incompatible conditions, including:

- real and mocked versions of the same module
- different import-time environment values
- different runtime or polyfill configuration
- fundamentally different implementations, such as local and remote storage

File boundaries combined with per-file isolation are a valid isolation mechanism. Prefer `storage-local.test.ts` and `storage-s3.test.ts` to cache-busting imports or repeated module replacement.

## Distinguish cleanup operations

- **Clear** removes recorded calls and results but preserves the implementation.
- **Reset** clears recorded state and resets the mock implementation. Use it when each test supplies its own behavior.
- **Restore** restores original implementations for spies and supported mocks. It does not remove `mock.module()` overrides or rebuild the module graph.

Use `try/finally` for critical cleanup of mutable globals because a failed assertion skips ordinary statements after it:

```ts
const originalFetch = globalThis.fetch;

try {
  globalThis.fetch = mockFetch;
  // exercise behavior
} finally {
  globalThis.fetch = originalFetch;
}
```

Restore `process.env` keys to their exact prior state, including deleting keys that were originally absent. Also clean up fake clocks, timers, `Date`, `console`, `Math.random`, global singletons, clients, and caches.

If code reads environment or global state during module initialization, prefer explicit configuration or separate test files instead of mutating it repeatedly in one file.

## Inspect import timing

Determine whether the mocked dependency or subject was loaded:

- before the mock registration
- after the registration
- through another imported dependency
- through a dynamic import
- in a preload script

Bun can update module exports after a module is imported, but effects from the original module have already run. Applying a mock does not undo initialization side effects or reconstruct every consumer's state. If the mock must prevent original initialization, register it in a preload script or import the subject dynamically after registration.

Do not use randomized query strings or similar cache-busting imports as a routine workaround.

## Diagnose order-dependent failures

For a test that passes alone and fails in a larger run:

1. Read the entire affected test file.
2. Search the relevant scope for every `mock.module(` call, especially calls inside tests and hooks.
3. Trace import order for the subject, mocked dependency, and intermediate importers.
4. Inspect `process.env`, globals, singletons, module caches, database clients, fake time, timers, and fetch replacements.
5. Verify that spies and function mocks use the intended clear, reset, or restore operation.
6. Run the smallest reproducer, then the surrounding suite.
7. Split incompatible import-time configurations into separate files.

Do not hide leakage by reordering tests or files, renaming files, retrying failures, disabling tests, adding arbitrary sleeps, or switching to serial execution without finding the shared state. Add teardown only after identifying the resource that needs cleanup.

## Use isolation deliberately

When the project and Bun version support it, use per-file isolation while investigating shared state:

```bash
bun test --isolate
bun test --parallel
```

`--parallel` implies `--isolate` in current Bun releases. Isolation gives each file a fresh JavaScript global and module registry. It does not make repeated within-file module replacement safe. Respect the repository's standard command and configuration; do not silently change them just to make a failure disappear.

## Validate changes

After changing Bun tests, run at least the changed file:

```bash
bun test path/to/changed.test.ts
```

Then run the nearest relevant package or project suite. For suspected cross-file leakage, compare the isolated or parallel run with the standard suite:

```bash
bun test
bun test --isolate
```

Do not declare a fix based only on the originally failing test passing by itself.

## Review by impact

Report concrete behavior and the smallest reproducer when possible. Prioritize findings as follows:

- **High:** per-test or per-hook replacement of the same module, dependence on execution order, or a test that passes alone and fails in the suite
- **Medium:** unrestored environment or global mutations, elaborate module mocking where a narrower boundary would work, or assertions coupled to private implementation details
- **Low:** repeated setup that a stateless helper could remove without hiding dependencies

## Consider another runner only when needed

Keep Bun if stable function mocks, dependency injection, or separate files solve the problem cleanly. Consider Vitest when the suite fundamentally requires repeated per-test module graph replacement, module resets, or sophisticated ESM isolation. Do not migrate before checking whether the test architecture can be simplified.

Use this decision path:

```text
Can a function or injected dependency be mocked?
  yes -> use that
  no  -> can one file-scoped mock.module() cover the file?
           yes -> mock once and vary function behavior
           no  -> can incompatible graphs be split into files?
                    yes -> split them
                    no  -> consider Vitest
```
