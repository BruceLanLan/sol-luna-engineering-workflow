# Luna task packet template

Copy this template for every delegation.

````markdown
## Objective

One concrete outcome.

## Relevant context

Only the repository facts and approved decisions needed for execution.

## In scope

- Files or modules Luna may inspect.
- Files or modules Luna owns for writing.

## Out of scope

- Files, systems, or decisions Luna must not change.

## Constraints

- Existing conventions to follow.
- No new dependencies unless explicitly listed.
- Compatibility, performance, or safety constraints.

## Acceptance criteria

- Observable behavior that must hold.
- Edge cases that must be covered.
- Exact limits on unintended changes.

## Required validation

```shell
# Exact targeted commands
```

## Expected return

1. Summary of changes.
2. Exact files changed.
3. Commands executed.
4. Result of each validation.
5. Remaining risks or uncertainty.
6. Decisions required from Sol.

## Escalate immediately if

- Requirements contradict repository behavior.
- A public interface or dependency must change.
- Security, data integrity, or backward compatibility is affected.
- Required tests cannot run.
- Two implementation attempts fail.
- Scope expands materially.
````

## Bad packet

```text
Fix the export feature and make it production-ready.
```

It does not define the behavior, scope, constraints, validation, or escalation boundary.

## Better packet

```text
Objective: Escape commas, quotes, CR, and LF in the existing CSV serializer.
In scope: src/export/csv.ts and tests/export/csv.test.ts.
Out of scope: download UI, API routes, dependencies, and public type changes.
Acceptance: RFC-style doubled quotes; existing output unchanged for plain fields;
new tests cover each special character and empty input.
Validation: npm test -- tests/export/csv.test.ts
Escalate if: current public API cannot preserve compatibility or tests contradict
the approved escaping behavior.
```
