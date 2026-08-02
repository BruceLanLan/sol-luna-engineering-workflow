# Luna worker task packet

```markdown
## Objective
One concrete, observable outcome.

## Relevant context
Only approved facts and decisions needed for execution.

## In scope
- Inspectable files/modules.
- Writable files/modules owned exclusively by this worker.

## Out of scope
- Files, systems, and decisions this worker must not change.

## Constraints
- Conventions, compatibility, safety, performance, and rollback requirements.
- No new dependencies unless explicitly authorized.

## Acceptance criteria
- Observable behavior and required edge cases.

## Required validation
Exact commands or deterministic checks.

## Expected return
1. Summary and exact files changed.
2. Commands/checks and each result.
3. Remaining risks or uncertainty.
4. Decisions required from the primary Luna thread.

## Escalate immediately if
- Repository facts contradict the packet.
- Interface, dependency, security, data-integrity, or compatibility decisions appear.
- Validation cannot run, scope expands, or two attempts fail.
```
