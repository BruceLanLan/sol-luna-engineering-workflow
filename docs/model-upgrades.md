# Model Upgrade and Local Sync Guide

Use this guide when updating this project for a newly released model and syncing the updated workflow configuration to local Codex.

## Confirm what is being updated

- **Upgrade a model:** change the model ID for the specified role and verify the supported reasoning effort.
- **“Update local Codex to use the new version”:** sync this project's latest model configuration, custom agents, and rules to the active local Codex configuration directory.
- Update the application or CLI installation only when the user explicitly asks for it. Configuration sync does not upgrade the app, update the CLI, or install other products.

For a configuration sync request, “latest version” means the latest version in this project. Use the model specified by the user; do not substitute another model on your own.

## Preserve the existing roles

| Use | Stable role | Current model ID | Current reasoning effort |
|---|---|---|---|
| Default primary thread | Primary session | `gpt-6-luna` | `max` |
| General execution subagent | `luna_worker` | `gpt-6-luna` | `max` |
| Difficult-decision advisor | `sol_advisor` | `gpt-6.1-sol` | `high` |
| Very difficult or repeatedly failing decision advisor | `astra_advisor` | `gpt-6-astra` | `high` |

Upgrading a model does not change Luna-first routing, role names, permissions, concurrency, task boundaries, or acceptance responsibilities. The user may manually select Sol High for the primary session; general execution subagents should still use the configured Luna Max.

## Standard procedure

### 1. Check the target model and current state

- Consult the current official documentation to confirm the target model's exact ID, supported reasoning efforts, and current Codex configuration fields. Preserve the model explicitly requested by the user.
- Check Git status, project configuration, local configuration, and the current session's model selection. Preserve existing changes.
- Distinguish model access, static configuration, and the model actually used at runtime. None is evidence for the others.

### 2. Change only the affected locations

| Upgrade target | Locations to check |
|---|---|
| Luna | The primary model, default subagent model, and reasoning effort in `.codex/config.toml`; `.codex/agents/luna-worker.toml` |
| Sol | `.codex/agents/sol-advisor.toml` |
| Astra | `.codex/agents/astra-advisor.toml` |
| Related documentation | `AGENTS.md`, English and Chinese READMEs, `docs/verification.md`, and the model table in this guide |

Check the current model names and IDs in these files, and replace only values affected by this upgrade. Preserve the existing Markdown structure, wording, and examples; do not rewrite whole documents or modify historical commits or session logs. Change reasoning effort only when the user requests it or the target model does not support the existing value, and record the reason.

### 3. Check the project files

- Follow the [verification guide](verification.md) to parse the TOML, confirm the target model and effort, and inspect each agent's `name`, `description`, and `developer_instructions`.
- Review the actual changes with `git diff` and check formatting with `git diff --check`.
- Passing static checks confirms only that the files are valid; it does not establish that the new model has run.

### 4. Sync the configuration used by local Codex

First determine the Codex configuration directory actually used by the app. It is usually `~/.codex`; if the runtime defines `CODEX_HOME`, use that directory. Do not mistake the standalone CLI installation directory for the app's configuration directory.

1. Back up the local `config.toml`, `AGENTS.md`, and the three project agent files. Record the backup paths so they can be restored; do not output credentials.
2. Merge the model, reasoning effort, and this project's `[agents]` configuration from the project `.codex/config.toml` into the local `config.toml`. Preserve other configuration, login information, providers, and tool settings; do not replace the entire file.
3. Sync the three files under the project `.codex/agents/` directory to the local `agents/` directory, preserving other agents.
4. Replace the managed content in the global `AGENTS.md` between these markers with the project `AGENTS.md` content, preserving personal rules outside the markers. If this is the first installation and the markers are absent, append the block:

   ```text
   <!-- sol-luna-engineering-workflow START -->
   Project AGENTS.md content
   <!-- sol-luna-engineering-workflow END -->
   ```

5. Check whether the global `AGENTS.override.md`, project rules, or session overrides affect what is loaded. If there is a conflict, report its location; do not delete other rules or change other projects without authorization.
6. Parse the local configuration again. The managed models and efforts should match the project, all three agent files should match individually, and other configuration should retain its original values.

### 5. Verify actual invocations

- Start a new task to load the updated configuration. Existing sessions retain the primary model selected by the user; syncing files does not rewrite their history or current turn.
- Verify the models involved in this upgrade. To confirm “Sol High primary session → Luna Max execution subagent,” start a minimal, read-only `luna_worker` from a Sol High session and inspect runtime model and reasoning-effort metadata for both.
- Only agent activity, tool results, or runtime records such as `turn_context.model` and `effort` prove which models actually ran. Agent self-reports and TOML values are not substitutes for runtime evidence.
- To verify an advisor, assign only one clearly stated decision question. Do not launch a full development task or costly work just to verify an upgrade.
- Record the primary model, subagent role, actual subagent model, reasoning effort, and any explicit spawn parameters. If the agent has not completed the check, report only the runtime evidence collected; do not claim the task is complete.

Global `AGENTS.md` guides whether to delegate and which role to choose; the default subagent configuration and custom agents provide model bindings. Explicit spawn parameters may still override defaults, so do not explicitly select an old Luna model for a general worker or silently fall back to one. If the target is unavailable, report the limitation and the proposed alternative.

### 6. Deliver

Report the roles changed, model IDs and efforts, configuration directory, sync checks, runtime evidence, and any incomplete work. Follow the user's authorization for Git operations. If the user asks to keep one commit, amend the specified commit when it has not been published and amending it is authorized. Do not rewrite published history or force-push automatically.

## Request template for the next upgrade

```text
Following docs/model-upgrades.md, upgrade <role> from <old model ID> to <new model ID>.
Preserve Luna-first routing, existing reasoning efforts, and the original Markdown; change only affected fields.
Verify the actual model, then sync this project's latest configuration to local ChatGPT/Codex.
Here, “update local Codex to use the new version” means sync this project's configuration.
<As needed: commit / amend the specified commit and keep one commit / do not commit.>
```

## Official references

- [Global and project AGENTS.md loading rules](https://developers.openai.com/codex/guides/agents-md/)
- [Multi-agent and model configuration](https://developers.openai.com/codex/multi-agent/)
- [Configuration reference](https://developers.openai.com/codex/config-reference/)

Consult the official documentation again for future upgrades. The model table in this guide reflects the current project configuration and does not guarantee future model IDs, fields, or account access.
