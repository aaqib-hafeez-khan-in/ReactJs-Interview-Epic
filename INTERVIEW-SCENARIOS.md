# React Interview Scenarios

Definitions are useful, but strong React interviews test whether you can reason about production behavior and trade-offs.

## Search-as-you-type

A search box requests data on every keystroke.

### Strong answer

- Debounce user input when appropriate.
- Prevent stale responses from replacing newer results.
- Cancel obsolete requests with `AbortController` where supported.
- Handle loading, empty, error and retry states.
- Consider caching and server rate limits.

### Follow-up

Why is debouncing alone insufficient?

Because an older request can finish after a newer request. Stale work must also be cancelled or ignored.

## Slow large list

A component renders thousands of rows and scrolling is slow.

### Strong answer

Measure first. Investigate render cost, expensive calculations, DOM size, layout/paint work, data updates and unnecessary child renders. Virtualization may be appropriate for very large lists.

### Trap

`React.memo` is not a universal performance fix. It does not eliminate a huge DOM or expensive layout work.

## Effect fires unexpectedly

### Debugging sequence

1. Identify every reactive value used by the effect.
2. Inspect the dependency array.
3. Check whether object or function dependencies are recreated.
4. Determine whether the effect is actually synchronizing with an external system.
5. Remove the effect if the value can be derived during render or handled by an event.

### Key point

Effects are for synchronization with external systems, not a general-purpose place for derived calculations.

## Hydration mismatch

A server-rendered application reports a hydration mismatch.

### Investigate

- Browser-only APIs used during the initial render.
- Random values or timestamps generated independently on server and client.
- Different server/client conditions.
- Invalid HTML nesting.
- Data changing between server rendering and hydration.

### Key point

Hydration reconciles client React with markup that already exists; it is not simply another client-side render.

## Inaccessible modal

A modal looks correct but fails keyboard accessibility.

### Strong answer

The dialog needs an accessible name and appropriate semantics. Keyboard users need reliable focus movement, operation and closing. Focus should not escape incorrectly and should normally be restored when the modal closes. Escape behavior should be considered.

A portal can also help place the modal outside problematic stacking, overflow or layout ancestors while keeping it in the same React tree.

## Context causes excessive rendering

A provider causes large parts of the application to re-render.

### Investigate

Check whether the provider value changes identity on every render and how widely the context is consumed. Consider splitting contexts, narrowing consumers, moving state ownership, or using a more targeted subscription model.

### Trap

Do not blindly add `useMemo`. First identify the actual propagation problem.

## Concurrent rendering exposes a bug

Look for side effects during render, mutable module-level state, timing-dependent reads, and code that assumes rendering occurs exactly once. Rendering should remain pure because React may start, pause, restart or abandon rendering work before committing it.

## Optimistic mutation

A slow mutation needs an optimistic UI.

### Strong answer

Explain the optimistic state, rollback strategy, failure handling, overlapping mutations, reconciliation with server truth and whether the framework or data layer already provides a suitable primitive.

A senior answer discusses failure modes rather than only the happy path.

## “How would you improve this React application?”

Start with evidence, not fashionable APIs:

1. Establish the user-facing problem.
2. Measure it with profiling and production telemetry.
3. Identify the dominant bottleneck.
4. Make the smallest change that addresses it.
5. Measure again.
6. Explain the trade-off and regression risk.

The goal is a UI that remains correct, accessible, observable and maintainable as it grows.

## Render storm after a small state change

A button updates one piece of state, but a large subtree re-renders and the interaction becomes slow.

### Investigate

Profile the interaction first. Identify which state owner changed, which children actually consume that state, whether context propagation is involved, and whether expensive work is repeated during render.

### Strong answer

Move state closer to the components that need it when possible. Split broad contexts, reduce unnecessary subscriptions, and only then consider memoization or other performance techniques where profiling shows a benefit.

### Follow-up

Does a component re-rendering prove the DOM changed?

No. Rendering recalculates the UI description; reconciliation determines whether host updates are required.

## Stale closure breaks an interaction

A timer, subscription or event handler uses an older state value even though the screen displays the latest value.

### Strong answer

Explain the closure that captured the older render's value. Choose the fix based on the operation: functional state updates for derived next state, correct dependencies for effects, or a ref when mutable latest-value access is genuinely required.

### Trap

Do not silence dependency warnings simply to stop an effect from running. First establish what value the synchronization actually depends on.

## Effect loop appears after adding a dependency

Adding a function or object to an effect dependency array causes repeated execution.

### Investigate

Determine whether the dependency is recreated during render, whether the effect is needed at all, and whether the external system is the real source of the required synchronization.

### Strong answer

Prefer restructuring the code to remove unnecessary effect dependencies over memoizing everything. Memoization is a tool for preserving identity when identity itself matters; it should not be used to conceal an architectural problem.

## Server/client boundary mistake

A modern React application fails because browser-only logic or non-serializable data crosses a server/client boundary.

### Strong answer

Identify which code must execute on the server and which requires client capabilities such as state, effects or browser APIs. Keep the boundary as small as practical and pass only data that the framework/runtime can safely transport.

### Senior-level point

The goal is not to make everything client-side. Server/client placement affects bundle size, latency, data access, security and caching behavior.

## Production interaction regression

A release increases interaction latency for a previously fast screen.

### Debugging sequence

1. Compare the regression against the release boundary.
2. Use profiling to identify expensive renders or calculations.
3. Check bundle and network changes.
4. Inspect data volume and cache behavior.
5. Check long tasks and main-thread contention.
6. Reproduce with realistic production-like data.

### Senior-level point

Optimize the measured bottleneck and define a regression guardrail rather than applying a generic optimization checklist.
