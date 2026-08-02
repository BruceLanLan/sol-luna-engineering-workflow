# Routing guide

## Decision sequence

1. Can Luna Max complete and verify the task safely in one thread? Use `LUNA_LOCAL`.
2. Are there at least two independent, disjoint, separately verifiable packets? Use `LUNA_PARALLEL` with `luna_worker`.
3. Does one high-impact decision remain after targeted evidence gathering? Use `SOL_ADVISED` for that question, then return execution to Luna.

## Difficulty is not size

A thousand mechanical edits can be large but easy. A ten-line authorization change can be small but difficult. Escalate based on uncertainty, blast radius, reversibility, and the cost of a plausible error—not file count.

## Examples

### LUNA_LOCAL

Add tests for established parser behavior and run the targeted suite.

### LUNA_PARALLEL

Implement an approved feature whose UI, serializer, and tests live in disjoint files. Assign one writable owner per packet; the primary Luna thread integrates and validates.

### SOL_ADVISED

A cache change may return stale authorization data. Luna first collects the call path, TTL configuration, and failing concurrency evidence. Sol Advisor decides the consistency policy and acceptance criteria. Luna implements the decision.

## Sol request shape

```text
Decision: Can the proposed cache policy expose stale authorization data?
Evidence: <call graph, TTL configuration, reproducer, test output>
Constraints: <compatibility and latency requirements>
Return: recommendation, rationale, rejected alternatives, risks,
implementation constraints, and acceptance criteria.
```

Do not ask Sol to “review everything” or “complete the task.”
