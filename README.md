# Alexus

**Don't take the AI's word for it.**

Two claims, each one shown in `demo/run-demo`:

1. **Alexus runs the checklist itself.** When the AI says "done", Alexus runs the checks. If one fails, the AI is sent back. A run is only marked proved when every check passed, run by Alexus, not by the AI.
2. **If anyone edits a receipt afterward, Alexus names the line.** Every step is written to a hash-chained log by Alexus, not by the AI.

What it does not claim: someone with full control of the machine can rewrite the whole log. Keep the head hash somewhere else (`verify --head`), and you'll catch that too.

Works with the Claude Code, Grok, and Codex CLIs. No keys stored. Your own AI accounts.

This program starts a Claude Code, Grok, or Codex session in a folder and keeps a live state file for that run. When a run is in proved mode, it does not trust the agent's own "done": it runs the checklist commands itself.

Alexus, by Ragnarok. "Don't ship probably. Ship provably."

Settings live in `~/.alexus/runner/config.json`, or in `$ALEXUS_HOME/config.json` when that variable is set. Run state lives next to that file under `agents/`. The settings file is owner-only (mode 600) and never holds keys, tokens, or passwords.

## Setup

Install at least one of these CLIs and put it on `PATH`: `claude`, `grok`, or `codex`. Then, from this folder:

```
bin/alexus setup
```

That lists what it found, asks for a display name and the order to use them, and writes the settings file. It does not ask for keys.

Non-interactive:

```
bin/alexus setup --owner Ada --who claude --handoff grok,codex --yes
```

If none of those CLIs is installed, setup says so and exits non-zero. `start` with no settings file prints one line telling you to run setup, and starts nothing.

Optional hooks (`after_leg`, `before_prove`, `receipt`, `auditor`) can be added to the settings file later. They are off by default.

## Plan, then start

Say what you want in your own words. A planner says it back. You revise it. A run starts only after that.

```
bin/alexus plan new --cwd DIR -- "task"
bin/alexus plan revise ID -- "what to change"
bin/alexus plan start ID
```

`alexus plan` is the front door. `plan new` asks the CLI from settings, or the one you name with `--who`. It writes UNDERSTOOD, WILL, WONT, optional WALLS, and a checklist. Each check is run once before any work. A check that already passes is marked as proving nothing. Saved plans live under `$ALEXUS_HOME/plans`.

Codex plans in its own read-only sandbox. Claude and Grok have no read-only mode, so they plan in a throwaway copy of the folder. The prompt names that copy, so they cannot change the real one.

`plan start` freezes that checklist and starts the worker with the worker and handoff from settings. `--who` on `plan start` picks the worker. With no walls, handoff stays on. Enforced walls run on Claude only and turn handoff off. The plan says that in plain words when it applies.

`plan new` prints JSON for scripts and the next steps for you. The plan you read is `PLAN.md` in the plan's folder.

Earlier failed runs on this install can be shown to the planner when the words overlap. That note is saved on the plan and the worker sees it in `PLAN.md`.

```
bin/alexus start --cwd DIR -- "task"
```

That starts nothing. It prints one line telling you to plan first.

`--no-plan` is the only skip. The worker then writes its own checklist, the same as proved mode without an approved plan. `--checklist FILE` still starts a run, and `plan start` uses it. `--domain FILE` does not start a run by itself. It can be combined with a checklist or with `--no-plan`.

`--who` and `--handoff` default to the settings file when you skip planning. `--plain` turns proved mode off.

## Self-check

From this folder, with `python3` on `PATH`:

```
bin/alexus selfcheck
```

That uses fake CLIs only. It does not call a model. It prints one `ok` line per case. A `FAIL` line means the check did not pass.

## Receipts

Every run writes a hash-chained log of what this program did, not what the agent said. The file is `$ALEXUS_HOME/agents/<id>/receipts.jsonl`. Each line is one receipt: a sequence number, a type, a small data object, the previous line's hash, and its own hash. Prompts, tool inputs, and tool outputs are stored only as hashes.

```
bin/alexus verify ID
bin/alexus verify ID --head HASH
bin/alexus receipts ID
bin/alexus receipts ID --html FILE
```

`verify` prints `intact` with the receipt count and head hash, or names the line and the reason the chain is malformed. `receipts` prints each checklist line as DONE or NOT DONE with the receipt behind it.

What this proves: if a line in the file was changed after it was written, `verify` names that line.

What this does not prove: someone who can rewrite the whole file can build a new valid chain. That is why `--head` exists. Keep the head hash somewhere else.

A stranger can watch the whole loop, with no model calls:

```
demo/run-demo
```
