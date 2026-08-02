# Verification and fallbacks

## Static checks

With Python 3.11 or newer:

```shell
python3 - <<'PY'
from pathlib import Path
import tomllib

config = tomllib.loads(Path('.codex/config.toml').read_text())
agent = tomllib.loads(Path('.codex/agents/luna-executor.toml').read_text())

assert config['model'] == 'gpt-5.6-sol'
assert config['model_reasoning_effort'] == 'medium'
assert config['agents']['default_subagent_model'] == 'gpt-5.6-luna'
assert config['agents']['default_subagent_reasoning_effort'] == 'medium'
assert agent['name'] == 'luna_executor'
assert agent['model'] == 'gpt-5.6-luna'
assert Path('.codex/config.toml').read_text().count('[agents]') == 1
print('Static configuration checks passed.')
PY
```

For Python 3.10 or earlier, use a TOML parser already available in your environment rather than installing a dependency solely for this check.

## Runtime smoke test

Start a new Codex task in the repository and send:

```text
Create docs/delegation-smoke-test.md containing exactly one line:
"Delegation smoke test passed."

Classify this task first. If project-level custom agents are supported, delegate
the edit to luna_executor. Validate the exact file content, then have Sol inspect
the diff and report final acceptance. Do not change any other file.
```

Evidence of success:

- Codex loads the project instructions.
- The route is `LUNA_DIRECT`.
- Execution is attributed to `luna_executor` using GPT-5.6 Luna.
- Luna returns its changed file and validation evidence.
- Sol independently inspects the diff and accepts or rejects it.

## What static validation cannot prove

Valid TOML proves syntax only. It does not prove that:

- your Codex build supports each configuration key;
- your account has access to both model IDs;
- the current interface loads project-level custom agents;
- an already-open task has reloaded changed project configuration.

Do not claim automatic delegation works until the runtime smoke test demonstrates it.

## Fallbacks

If project-level custom agents are unavailable:

1. Keep Sol as the main task and use the same routing rules manually.
2. Create or invoke a Luna task/subagent explicitly for each bounded packet if your interface supports per-task model selection.
3. Paste the packet from `docs/task-packet.md` into the Luna task.
4. Return the evidence to Sol for independent review and acceptance.

If GPT-5.6 Luna is unavailable, choose a supported execution model explicitly and preserve the same packet, escalation, and acceptance boundaries. If GPT-5.6 Sol is unavailable, use the strongest supported supervisor model you can access and document the substitution.
