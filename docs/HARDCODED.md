# What is still hardcoded

Stage 3 replaced person-specific names, private paths, the old environment prefix, and the private-install refusal list with a settings file at `$ALEXUS_HOME/config.json` (default `~/.alexus/runner/config.json`) plus setup. This file lists only what is still baked into the program.

## Muse

`muse` is not a CLI a stranger can install, so it is not a listed provider. `setup` does not offer it and a new settings file does not include it. `--who muse` still runs the existing Muse stream parser when the `muse` binary is on `PATH`.

## macOS-only programs

These paths assume a Mac. On another host they need a replacement; the program does not pretend `sandbox-exec` exists everywhere.

- `sandbox-exec` proved-mode check profile in `SANDBOX`: writes allowed only under `/private/tmp` and `/private/var/folders`; `ssh`, `scp`, `sftp`, and `rsync` denied.
- Walled Claude shell hook uses `/usr/bin/python3`, `#!/bin/bash`, `/usr/bin/sandbox-exec`, and `/bin/zsh`.
- Checklist commands run under `sandbox-exec`, then `/usr/bin/sandbox-exec`, then `/bin/zsh -c`.
- Stop compares a pid's start time with `/bin/ps -o lstart=` (BSD `ps`).
- The critic's throwaway copy uses `cp -cR` (APFS clone) and falls back to a normal copy when that flag is not supported.

## Planning

- Saved plans live under `$ALEXUS_HOME/plans`. The planner reuses this program's checklist parser and sandboxed check runner.
- Only the Codex planner is held by a provider read-only flag (`codex exec --sandbox read-only`). It is not wrapped in another seatbelt, so that flag can apply its own sandbox. Claude and Grok have no such flag. They plan in a throwaway copy of the folder. The prompt names that copy, not the real folder, so a planner that writes where it was pointed does not change the real folder when `sandbox-exec` cannot be applied. When a profile can be applied, the process is also denied writes to the real folder. The planner does not change the task folder's permissions. A Python import hook on a sandboxed planner process also refuses writes under the real folder. That hook is not a provider read-only mode, and the program does not record one for Claude or Grok. If the copy fails, Claude and Grok do not plan in the real folder. A revise keeps past failures already saved on the plan. A revise that fails does not change the provider recorded on the plan.
- Keyword overlap ignores the account name in a `/Users/<name>` or `/home/<name>` path. The folder counts only as its own last name, so a shared parent such as `Projects` is not a match, and one shared task word is not a match. A run whose verdict is proved is not shown as a past failure.
- Enforced walls are still Claude-only and turn handoff off. A plan with those walls says so, and `plan start` obeys it. With no enforced walls, handoff stays on and comes from settings. `plan edit --no-walls` keeps walls off through a later revise.
- Before a plan is written, failed runs under `$ALEXUS_HOME/agents` are searched by keyword overlap with the request and the folder. No embeddings and no network. At most 3 matches and at most 1500 characters are shown to the planner and saved on the plan. The worker sees them in `PLAN.md`.
- Checklist commands and walls are stored relative to the task folder. The throwaway copy's absolute path is removed before the plan is saved, because that copy is deleted.
- A revise starts a new planner call and passes the request and the plan so far in the prompt. It does not resume the old conversation, because that conversation belonged to a copy that no longer exists.
- A run counts as a past failure only if it did not work. Proved runs, runs waiting on a person, and plain runs that finished normally are left out. Private keys and API tokens are removed from past-failure text before it is shown or saved.
- A saved model name is used only with the provider that planned, or on a hand-written plan. `alexus plan` is the only way to call the planner. `bin/alexus-plan` is a module, not a command.
- `plan new` and `plan revise` print JSON on stdout. The plain next steps, and the walls sentence when it applies, go to stderr.

## Other leftovers that are not personal

- Muse MSP stream shapes (`payload_type`, `assistant_tool_calls_committed`, and the rest of `apply_muse_event`) stay as the CLI's stream contract.
- `antigravity` is still stripped from old handoff chains so a saved record cannot select a removed runner.
- The shebang is `#!/usr/bin/env python3`.
