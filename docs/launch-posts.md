# Launch posts

Repo link in these: https://github.com/ORGRGNRK/alexus-app (works once the repo is public).
Attach a screen recording of the demo page, or of `demo/run-demo` in a terminal.
Only two claims. Don't add more.

---

## X / Threads (one post)

I got tired of AI agents saying "done" when they weren't.

So I built Alexus. It doesn't take the AI's word. It runs the checks itself.

In the demo the AI says it fixed a bug. It hadn't. Alexus's own check fails, sends it back, and only marks it proved after the check passes.

Every step is a hash-chained receipt. Edit one character later and it names the line.

Works with Claude Code, Grok and Codex. Your own accounts.

github.com/ORGRGNRK/alexus-app

---

## Reddit (AI coding communities)

**Title:** I built a runner that doesn't trust the AI's "done" and checks the work itself

I use Claude Code, Grok and Codex every day, and the part that kept landing on me was checking whether "done, tests pass" was actually true. Too often it wasn't.

So I built Alexus. You give it a task and a checklist. The AI works. When it says done, Alexus runs the checklist itself. If a check fails, the AI goes back to work. A run only gets marked proved when every check passed, run by Alexus.

Everything it does goes into a hash-chained receipt log, written by the runner, not the AI. If anyone edits a receipt afterward, `alexus verify` names the line.

What it doesn't do: if someone has full control of your machine they can rewrite the whole log. You can keep the head hash somewhere else and it catches that too.

Demo runs with no AI calls: `demo/run-demo`. In it, the AI claims it fixed `add.py` before it had. Receipt #4 FAILED, sent back, receipt #7 PASSED, proved.

It uses your own CLIs and accounts and stores no keys. Early, rough in places, macOS first.

Repo: github.com/ORGRGNRK/alexus-app

Would love to hear where it breaks for you.

---

## Hacker News

**Title:** Show HN: Alexus – an agent runner that checks the AI's "done" itself

Alexus starts a Claude Code, Grok or Codex session on a task with a checklist. When the agent says it's finished, Alexus runs the checklist commands itself and sends the agent back if any fail. A run is "proved" only when every check passed, run by the runner.

Each run writes a hash-chained receipts.jsonl (prompts and outputs stored as hashes). `alexus verify` reports intact, or names the first line that doesn't recompute. A full rewrite still chains, so `verify --head` takes a head hash you kept elsewhere.

`demo/run-demo` shows the loop with fake CLIs, no model calls: early "done" is caught, a one-character edit is named, a mid-run corruption ends as chain_broken.

macOS first (sandbox-exec, BSD ps). Uses your own CLIs, stores no keys.

https://github.com/ORGRGNRK/alexus-app

---

## LinkedIn

AI agents are good at saying "done." They're less good at being done.

I built Alexus so I'd stop being the one who checks. It runs the checklist itself when the AI says it's finished, sends it back when a check fails, and keeps a receipt for every step that shows if anything was edited later.

Don't ship probably. Ship provably.

github.com/ORGRGNRK/alexus-app
