# Verification and fallbacks

## Static check

```shell
python3 - <<'PY'
from pathlib import Path
import tomllib

config = tomllib.loads(Path('.codex/config.toml').read_text())
luna = tomllib.loads(Path('.codex/agents/luna-worker.toml').read_text())
sol = tomllib.loads(Path('.codex/agents/sol-advisor.toml').read_text())

assert config['model'] == 'gpt-5.6-luna'
assert config['model_reasoning_effort'] == 'max'
assert config['agents']['default_subagent_model'] == 'gpt-5.6-luna'
assert config['agents']['default_subagent_reasoning_effort'] == 'max'
assert luna['name'] == 'luna_worker' and luna['model'] == 'gpt-5.6-luna'
assert luna['model_reasoning_effort'] == 'max'
assert sol['name'] == 'sol_advisor' and sol['model'] == 'gpt-5.6-sol'
print('Static configuration checks passed.')
PY
```

## Runtime checks

Start a new task after installing the files.

1. Ask for one small bounded edit. Confirm the primary task identifies GPT-5.6 Luna Max and does not spawn Sol.
2. Ask for two independent read-only checks or disjoint edits. Confirm execution is attributed to `luna_worker` and files have one writable owner.
3. Present a deliberately ambiguous, high-impact design question. Confirm `sol_advisor` receives only the decision question and evidence, then Luna resumes execution.

Static TOML validation cannot prove model access or runtime loading. Report actual model use only when Agent activity or tool output identifies it.

## Fallbacks

- If custom agents are unavailable, select Luna Max as the main model and request Sol manually only for escalation cases.
- If Luna Max is unavailable, use the highest supported Luna effort and disclose the substitution.
- If Sol is unavailable, stop for decisions where its review is required or explicitly document the alternate advisor model.
- If parallelism adds more coordination than value, use `LUNA_LOCAL`.
