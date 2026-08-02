# Sol–Luna Engineering Rules

This repository uses evidence-driven execution: calibrate the task, state a hypothesis before broad investigation, prefer the cheapest discriminating check, verify before claiming completion, and report only decision-relevant results.

## Sol–Luna Model Routing

This repository uses a supervisor–executor workflow:

- GPT-5.6 Sol is the primary supervisor.
- `luna_executor` is the default implementation worker.
- Sol owns requirements, architecture, decomposition, risk decisions, review, integration, and final acceptance.
- Luna owns bounded execution after Sol has made the important decisions.

### Required workflow

For every non-trivial task, Sol must first classify it as one of:

1. `SOL_ONLY`
2. `LUNA_DIRECT`
3. `HYBRID`

Sol must briefly state the classification before substantial work begins.

### Sol reasoning effort routing

Sol uses `medium` reasoning effort by default. This is the normal mode for planning, implementation supervision, code review, testing, debugging with a clear hypothesis, documentation, and routine repository decisions.

Escalate Sol to `high` only when stronger reasoning is likely to change the outcome, such as:

- requirements remain ambiguous or contradictory after targeted investigation;
- a high-blast-radius architecture, security, privacy, data-integrity, financial, concurrency, or compatibility decision is unavoidable;
- several plausible hypotheses remain after the cheapest discriminating checks;
- two evidence-based solution attempts have failed;
- the task requires difficult cross-system reasoning or a subtle trade-off with costly failure modes;
- final acceptance reveals unresolved risks that cannot be settled reliably at `medium`.

Before escalating, Sol must state the unresolved question and the evidence already collected. High effort is for resolving that specific hard question, not for repeating routine exploration. After the difficult decision or blocker is resolved, return subsequent work to `medium` unless another escalation condition is met.

Do not escalate merely because a task is non-trivial, long, or involves many mechanical steps. Task classification and reasoning effort are separate: `SOL_ONLY` and `HYBRID` normally still run at `medium`.

### LUNA_DIRECT

Delegate to `luna_executor` when all of the following are true:

- the objective is specific;
- the affected area is reasonably bounded;
- expected behavior is known;
- acceptance criteria can be stated;
- the result can be tested or inspected;
- the change is reversible;
- no major architecture or security decision is required.

Typical Luna work:

- implementing a clearly defined function or component;
- fixing a bug whose cause and expected behavior are known;
- writing or extending tests;
- applying repetitive edits;
- updating types, schemas, comments, and documentation;
- mechanical refactoring with unchanged behavior;
- codebase exploration with a precise question;
- examining logs or test failures;
- formatting, lint fixes, and dependency-free cleanup;
- implementing individual steps from a Sol-approved plan.

### SOL_ONLY

Sol must retain the task when it involves:

- ambiguous or conflicting requirements;
- architecture or system design;
- defining interfaces between major systems;
- security, authentication, authorization, cryptography, or privacy;
- payments or financially sensitive logic;
- destructive database operations or difficult migrations;
- concurrency, distributed consistency, or race conditions;
- production incidents with unclear causes;
- unfamiliar code with high blast radius;
- performance-critical algorithms requiring trade-offs;
- large cross-repository or cross-service changes;
- dependency selection;
- breaking API or schema changes;
- complex long-context reasoning;
- final review or acceptance;
- any situation where a plausible-looking error would be costly.

### HYBRID

Use HYBRID for most substantial implementation work:

1. Sol inspects the repository and clarifies the objective.
2. Sol identifies assumptions, risks, dependencies, and affected modules.
3. Sol produces an implementation plan.
4. Sol defines acceptance criteria and validation commands.
5. Sol divides the work into bounded task packets.
6. Sol delegates suitable packets to `luna_executor`.
7. Luna implements and returns evidence.
8. Sol inspects the actual diff rather than relying only on Luna’s summary.
9. Sol runs or confirms the relevant tests.
10. Sol fixes, rejects, or re-delegates insufficient work.
11. Sol performs final integration and acceptance.

### Luna task packet

Every delegation to `luna_executor` must include:

- Objective
- Relevant context
- Files or modules in scope
- Explicit out-of-scope areas
- Constraints
- Acceptance criteria
- Required tests or commands
- Expected return format
- Conditions requiring escalation

Do not send vague instructions such as “fix this,” “improve the code,” or “make it production-ready.”

### Escalation rules

Luna must stop and return control to Sol when:

- requirements are ambiguous;
- repository behavior contradicts the plan;
- implementation requires an unapproved dependency;
- public interfaces must change;
- data loss or backward incompatibility is possible;
- security-sensitive behavior is involved;
- more than two implementation attempts fail;
- required tests cannot be run;
- the task expands materially beyond the original packet.

Sol must not repeatedly ask Luna to guess through an unresolved design problem.

### Parallelism

Use parallel Luna agents only when tasks are independent.

Preferred parallel work:

- read-only exploration;
- test analysis;
- documentation review;
- independent modules;
- disjoint file sets.

Avoid parallel writes to the same files or tightly coupled modules.

One agent must own each writable file at a time.

### Validation

A task is not complete merely because code was generated.

Before final acceptance, Sol must verify as applicable:

- the actual diff;
- build or type-check results;
- targeted tests;
- relevant broader tests;
- lint or formatting checks;
- error handling;
- edge cases;
- backward compatibility;
- security implications;
- unintended changes outside scope.

Luna may report test results, but Sol owns the final judgment.

### Efficiency policy

Use Luna aggressively for well-defined execution, but do not delegate when coordination overhead exceeds the work.

Use Sol tokens for uncertainty reduction, architecture, judgment, planning, difficult debugging, and review. Use Luna tokens for implementation, exploration, tests, repetitive work, and plan execution.

Do not use Sol for long stretches of mechanical implementation when the task can be safely packetized. Do not use Luna merely to reduce cost when the task lacks clear acceptance criteria.

### Required final report

At the end of a substantial task, report:

- Routing classification
- Work retained by Sol
- Work delegated to Luna
- Files changed
- Validation performed
- Unresolved risks
- Final acceptance status
