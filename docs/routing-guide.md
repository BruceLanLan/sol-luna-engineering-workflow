# Routing guide

## Decision sequence

Ask these questions in order:

1. Is the requirement ambiguous, conflicting, security-sensitive, destructive, or architecturally unresolved? Use `SOL_ONLY`.
2. Are the objective, scope, expected behavior, acceptance criteria, validation, and rollback all clear? Use `LUNA_DIRECT`.
3. Can Sol resolve the important decisions and split the remaining work into bounded packets? Use `HYBRID`.
4. If none applies cleanly, Sol investigates until one does. Luna must not be asked to guess.

## Examples

### LUNA_DIRECT

**Request:** Add unit tests for the existing `parseDate` behavior, including leap day and invalid input.

Why: expected behavior exists, the module is bounded, acceptance is executable, and no design decision is needed.

### HYBRID

**Request:** Add CSV export to an existing reporting feature.

Sol decides the public interface, authorization behavior, file limits, escaping rules, and test plan. Luna implements separate UI, serializer, and test packets in disjoint files. Sol inspects the integrated diff and validates end-to-end behavior.

### SOL_ONLY

**Request:** Redesign tenant authorization while preserving backward compatibility.

Why: security boundaries, public behavior, migration risk, and cross-system interfaces require supervisor judgment. Sol may later delegate mechanical packets after the design is approved.

## Reasoning effort

Use Sol `medium` for normal planning, routing, review, and evidence-driven debugging. High effort is warranted only when a specific unresolved question remains after targeted checks and the cost of a plausible error is high.

Escalation should look like this:

```text
Unresolved question: Can the new cache policy return stale authorization data?
Evidence collected: call graph, cache TTL configuration, two failing concurrency tests.
Why high is justified: this is a security and consistency boundary with competing fixes.
```

Do not say “use high because the task is big.” Size creates more packets; uncertainty and risk justify stronger reasoning.

## Parallel execution

Parallel Luna agents are useful only when work is independent. Good examples are read-only investigation, separate test suites, independent modules, and disjoint documentation. Avoid parallel writes to the same files or tightly coupled modules.

Maintain an ownership map:

| Packet | Owner | Writable files |
|---|---|---|
| API tests | Luna A | `tests/api/**` |
| UI component | Luna B | `src/components/export/**` |
| Integration and acceptance | Sol | No concurrent writes |
