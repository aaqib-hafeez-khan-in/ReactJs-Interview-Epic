# Advanced React Interview Drills

These prompts are designed for senior interviews where the candidate must reason about correctness, rendering, browser behavior, architecture and production trade-offs.

## 1. Search results arrive out of order

A search UI fires requests as the user types, and an older response overwrites a newer result.

### Strong answer

Debounce where appropriate, cancel obsolete requests with `AbortController`, and independently guard against stale responses. The UI should associate a response with the request that produced it and ignore results that are no longer current.

### Follow-up

Why is cancellation alone not a complete correctness strategy?

Because cancellation is not guaranteed to stop every already-started operation or server-side effect. The UI still needs stale-result protection.

## 2. Optimistic update conflicts with another mutation

A user toggles a setting quickly twice while the first request is still pending.

### Strong answer

Define ordering semantics first. Track mutation identity or sequence, update the UI optimistically, reconcile with server truth, and handle rollback without allowing an older response to overwrite a newer intent.

### Senior-level point

Optimistic UI is a state-management problem as much as a rendering problem.

## 3. Context value causes an application-wide render fan-out

A small state change causes dozens of unrelated components to render.

### Strong answer

Measure the propagation path. Split broad contexts by concern, move state ownership closer to consumers, or introduce a more targeted subscription model. Preserve referential stability only where it addresses a demonstrated issue.

### Trap

Do not start by wrapping everything in `useMemo` and `useCallback`.

## 4. Large table becomes unusable at 50,000 rows

The screen is correct but scrolling and filtering are slow.

### Strong answer

Measure render time, DOM size, filtering cost, memory use and browser long tasks. Use virtualization when the visible-window model fits the UX, keep expensive computations out of render where appropriate, and consider server-side filtering/pagination when the dataset is genuinely large.

### Trade-off

Virtualization improves DOM cost but introduces complexity around measurement, accessibility, keyboard interaction and variable row heights.

## 5. Hydration mismatch appears only in production

The server-rendered markup sometimes differs from the first client render.

### Strong answer

Look for nondeterministic values, browser-only APIs, locale/time-zone differences, changing data and invalid HTML nesting. Make the initial render deterministic and defer client-only behavior until the appropriate lifecycle or client boundary.

## 6. A component fetches data in an effect and flashes incorrect state

A route loads with stale data, then updates several times after mount.

### Strong answer

Question whether the effect is the correct data-fetching boundary. Separate server data from client synchronization concerns, model loading/error/stale states explicitly, and avoid effect-driven derived state when the value can be computed directly.

### Senior-level point

The right fix may be architectural: move fetching to the framework/data layer rather than adding more effect coordination.

## 7. Browser memory grows during navigation

Heap usage increases after repeatedly mounting and unmounting a screen.

### Strong answer

Check for event listeners, subscriptions, timers, observers, retained closures and third-party resources that are not cleaned up. Use browser heap snapshots and allocation analysis to identify retaining paths instead of assuming React itself is leaking.

## 8. A Server/Client boundary makes the bundle unexpectedly large

A small interactive component causes a large dependency subtree to become client-side.

### Strong answer

Trace the import graph and boundary. Keep client components focused on genuinely interactive code and avoid pulling server-oriented or large libraries across the boundary unnecessarily.

### Trade-off

A smaller client bundle may require moving data access or computation server-side and changing component boundaries; optimize the whole request path rather than bundle size alone.

## 9. Accessible modal works with a mouse but fails keyboard tests

The dialog opens correctly visually, but focus is lost and keyboard users can tab behind it.

### Strong answer

Use proper dialog semantics and accessible naming, move focus into the dialog, constrain keyboard navigation appropriately while open, support expected close behavior, and restore focus to the invoking control. Test with keyboard-only navigation and assistive technology expectations rather than relying on visual inspection.

## 10. Production interaction latency increases after adding a feature

The UI feels sluggish, but backend latency is unchanged.

### Strong answer

Check client-side profiling, long tasks, render frequency, expensive calculations, bundle changes, hydration cost, data volume and main-thread contention. Compare the release boundary and reproduce with realistic data.

### Senior-level point

A good optimization plan defines a measurable interaction budget or guardrail and verifies the change after implementation.
